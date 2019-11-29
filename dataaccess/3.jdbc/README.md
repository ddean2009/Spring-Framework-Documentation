# 3.JDBC

Spring Framework JDBC抽象提供的值可能最好通过下表中列出的操作序列来显示。 该表显示了Spring负责的操作以及你负责的操作。

| Action | Spring | You |
| :--- | :--- | :--- |
| Define connection parameters. |  | x |
| Open the connection. | x |  |
| Specify the SQL statement. |  | x |
| Declare parameters and provide parameter values |  | x |
| Prepare and execute the statement. | x |  |
| Set up the loop to iterate through the results \(if any\). | x |  |
| Do the work for each iteration. |  | x |
| Process any exception. | x |  |
| Handle transactions. | x |  |
| Close the connection, the statement, and the resultset. | x |  |

Spring框架负责所有可能使JDBC成为乏味的API的低级细节。

