# Multithreaded-TCP-server-with-multiple-database-connections-pool

TCP servers commonly have backend database.

In this example code, a multithreaded TCP server maintains multiple database connections. The multithreaded TCP server should able to accept multiple incoming client connections concurrently using server’s ephemeral ports and should able to R/W data from/to backend database concurrently using DB’s ephemeral ports.

Our TCP server and DB program are two separate computer programs. Both programs can be resided in the same computer or in separate computers. In this example, the TCP server & DB are in the same computer (localhost).

R/W data to database take times. Therefore, preferably to have multiple DB connections and multiple server-clients web-sockets concurrently to improve the DB efficiency and responsiveness to clients.

In simple SQL query procedure, process need to first establish connection with DB before the SQL query. The connection string includes login DB username and password, DB’s IP address, selected DB name, port number & etc. After finished one SQL query, process will close the connection with DB. This process will repeatedly login & logout DB for every SQL query to DB.

To avoid frequently open & close connections to DB, this example uses the connection pool idea. A multithreaded TCP server login & maintains multiple connections to DB using multiple threads & DB ephemeral ports. When no query to DB, the DB connections will be put into wait state to idle. Connection pool idea can be used in any type of connections that are allowed to be alive for extended period by the host; we can create multiple concurrent connections to the same source if permitted and if it would improve the efficiency.

Multithreaded TCP server-client code portion using the previous Example 3.

In TCP server-to-DB coding portion, we will have a shared queue to save all the clients’ DB queries. Established DB connection threads will execute the SQL queries whenever there are any in the shared queue. If there aren’t any in the shared queue, shared queue will make DB connection threads to idle in Wait state. TCP server connections to DB are retained in Wait state. When new SQL queries are added into the shared queue, shared queue will invoke an idle DB connection thread to R/W data to DB. TCP server-to-DB code reside in TCP server program.

Worker server-client threads and DB connection threads are separate threads. When worker server-client thread query DB via DB connection thread, worker server-client need to wait for DB connection thread to get DB query result & notify him.

In this example, client establish a new TCP connection with server for every new query to the server. Server close the TCP connection after sent the client the query result.

In this example, a connection to DB that does not last more than 1 minute is considered an exceptional connection error. When exceptional connection errors count more than 10 times, reattempt to connect stop.

 

c# multithreaded TCP server with database connections pool Console App:   [ServerWithDB_multithreaded.cs.txt](https://github.com/lksark/Multithreaded-TCP-server-with-multiple-database-connections-pool/blob/main/ServerWithDB_multithreaded.cs.txt)

c# TCP client querying DB Windows Forms App:    [webDB_Client.zip](https://github.com/lksark/Multithreaded-TCP-server-with-multiple-database-connections-pool/blob/main/webDB_Client.zip)

MS SQL Server MDF database backup file:   [db_Employee_Attendance.bak](https://github.com/lksark/Multithreaded-TCP-server-with-multiple-database-connections-pool/blob/main/db_Employee_Attendance.bak)
