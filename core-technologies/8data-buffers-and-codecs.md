# 8.数据缓存和解码器

Java NIO提供ByteBuffer，但许多库在顶部构建自己的字节缓冲区API，尤其是对于重用缓冲区和/或使用直接缓冲区有利于性能的网络操作。 例如，Netty具有ByteBuf层次结构，Undertow使用XNIO，Jetty使用pooled字节缓冲区并释放回调，依此类推。 spring-core模块提供了一组抽象来处理各种字节缓冲区API，如下所示：

* DataBufferFactory抽象创建数据缓冲区。
* DataBuffer表示可以合并的字节缓冲区。
* DataBufferUtils为数据缓冲区提供实用程序方法。
* Codecs将流数据缓冲流解码或编码为更高级别的对象。

## 8.1 DataBufferFactory

DataBufferFactory用以下两种方式之一创建数据缓冲区：

1. 分配一个新的数据缓冲区，可选择预先指定capacity（如果已知），即使DataBuffer的实现可以按需增长和缩小，这也更有效。
2. 包装现有的byte \[\]或java.nio.ByteBuffer，它使用DataBuffer实现来修饰给定的数据，并且不涉及分配。

请注意，WebFlux应用程序不直接创建DataBufferFactory，而是通过ServerHttpResponse或客户端的ClientHttpRequest访问它。 工厂类型取决于底层客户端或服务器，例如 Reactor Netty的NettyDataBufferFactory，其他的DefaultDataBufferFactory。

## 8.2 DataBuffer

DataBuffer接口提供与java.nio.ByteBuffer类似的操作，但也带来了一些额外的好处，其中一些受Netty ByteBuf的启发。 以下是部分列表：

* 使用独立位置进行读写，即不需要调用flip（）来在读写之间交替。
* 与java.lang.StringBuilder一样，按需扩展容量。
* 通过PooledDataBuffer汇集缓冲区和引用计数。
* 以java.nio.ByteBuffer，InputStream或OutputStream的形式查看缓冲区。
* 确定给定字节的索引或最后一个索引。

## 8.3 PooledDataBuffer

正如Javadoc for ByteBuffer中所解释的，字节缓冲区可以是直接缓冲区，也可以是非直接缓冲区。直接缓冲区可以驻留在Java堆之外，这样就无需复制本机I / O操作。这使得直接缓冲区对于通过套接字接收和发送数据特别有用，但是创建和释放它们也更加昂贵，这导致了pooling缓冲区的想法。

PooledDataBuffer是DataBuffer的扩展，它有助于引用计数，这对于字节缓冲池是必不可少的。它是如何工作的？当分配PooledDataBuffer时，引用计数为1.调用retain（）递增计数，而对release（）的调用则递减计数。只要计数大于0，就保证缓冲区不被释放。当计数减少到0时，可以释放pooled缓冲区，这实际上可能意味着缓冲区的保留内存返回到内存池。

请注意，不是直接对PooledDataBuffer进行操作，在大多数情况下，最好使用DataBufferUtils中的方法，只有当它是PooledDataBuffer的实例时才应用release或retain到DataBuffer。

## 8.4 DataBufferUtils

DataBufferUtils提供了许多用于操作数据缓冲区的实用方法：

* 将数据缓冲区流加入单个缓冲区中，可能只有零拷贝，例如：如果底层字节缓冲区API支持可以复合缓冲区。
* 将InputStream或NIO Channel转换为Flux，反之亦然，将Publisher转换为OutputStream或NIO Channel。
* 如果缓冲区是PooledDataBuffer的实例，则释放或保留DataBuffer的方法。
* 从字节流中跳过或取出，直到特定的字节数。

## 8.5 Codecs

org.springframework.core.codec 包提供了下面的策略接口：

* Encoder 用来将Publisher 编码为数据缓存的stream。
* Decoder 将Publisher解码为更高级别的对象流。

spring-core模块提供byte\[\]，ByteBuffer，DataBuffer，Resource和String编码器和解码器实现。 spring-web模块增加了Jackson JSON，Jackson Smile，JAXB2，Protocol Buffers和其他编码器和解码器。 请参阅WebFlux部分中的编解码器。

## 8.6 使用DataBuffer

使用数据缓冲区时，必须特别注意确保缓冲区被释放，因为它们可能被pooled。我们将使用codecs来说明它是如何工作的，但概念更普遍适用。让我们看看codecs在内部管理数据缓冲区时做了什么。

Decoder是在创建更高级别对象之前，最后一个读取输入数据缓冲区的，因此必须按如下方式释放它们：

1. 如果Decoder只是读取每个输入缓冲区并准备立即释放它，它可以通过DataBufferUtils.release（dataBuffer）来实现。
2. 如果Decoder正在使用Flux或Mono运算符（如flatMap，reduce等）在内部预取和缓存数据项，或者正在使用filter，skip和其他省略项的运算符，那么doOnDiscard\(PooledDataBuffer.class, DataBufferUtils::release\)必须被添加到组合链中以确保在丢弃之前（错误或取消信号）释放这些缓冲区，。
3. 如果Decoder以任何其他方式保持一个或多个数据缓冲区，则必须确保在完全读取时（或者在读取和释放高速缓存数据缓冲区之前发生错误或取消信号）释放它们。

请注意，DataBufferUtils\#join提供了一种安全有效的方法，可将数据缓冲区流聚合到单个数据缓冲区中。同样，skipUntilByteCount和takeUntilByteCount是decoders使用的其他安全方法。

Encoder分配其他人必须读取（和释放）的数据缓冲区。所以Encoder没什么可做的。但是，如果在使用数据填充缓冲区时发生序列化错误，则Encoder必须注意释放数据缓冲区。例如：

```java
DataBuffer buffer = factory.allocateBuffer();
boolean release = true;
try {
    // serialize and populate buffer..
    release = false;
}
finally {
    if (release) {
        DataBufferUtils.release(buffer);
    }
}
return buffer;
```

Encoder的使用者负责释放它接收的数据缓冲区。 在WebFlux应用程序中，Encoder的输出用于写入HTTP服务器响应或客户端HTTP请求，在这种情况下，释放数据缓冲区是代码写入服务器响应或客户端的责任。

请注意，在Netty上运行时，可以使用调试选项来排除缓冲区泄漏。

