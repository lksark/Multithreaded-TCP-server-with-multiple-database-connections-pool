# Multithreaded Server with multiple database connections pool

Servers commonly have backend database.

In this example code, a multithreaded server maintains multiple database connections. The multithreaded server should able to accept multiple incoming client connections concurrently using server’s ephemeral ports and should able to R/W data from/to backend database concurrently using DB’s ephemeral ports.

Our TCP server and DB program are two separate computer programs. Both programs can be resided in the same computer or in separate computers. In this example, the TCP server & DB are in the same computer (localhost).

R/W data to database take times. Therefore, preferably to have multiple DB connections and multiple server-clients web-sockets concurrently to improve the DB efficiency and responsiveness to clients.

In simple SQL query procedure, process need to first establish connection with DB before the SQL query. The connection string includes login DB username and password, DB’s IP address, selected DB name, port number & etc. After finished one SQL query, process will close the connection with DB. This process will repeatedly login & logout DB for every SQL query to DB.

To avoid frequently open & close connections to DB, this example uses the connection pool idea. A multithreaded server application login & maintains multiple connections to DB using multiple threads & DB ephemeral ports. When no query to DB, the DB connections will be put into wait state to idle. Connection pool idea can be used in any type of connections that are allowed to be alive for extended period by the host; we can create multiple concurrent connections to the same source if permitted and if it would improve the efficiency.

Multithreaded server-client code portion using the previous Example 3.

In server application-to-DB coding portion, we will have a shared queue to save all the clients’ DB queries. Established DB connection threads will execute the SQL queries whenever there are any in the shared queue. If there aren’t any in the shared queue, shared queue will make DB connection threads to idle in Wait state. Server application’s TCP connections to DB are retained in Wait state. When new SQL queries are added into the shared queue, shared queue will invoke an idle DB connection thread to R/W data to DB. Server application-to-DB code reside in server application.

Worker server-client threads and DB connection threads are separate threads. When worker server-client thread query DB via DB connection thread, worker server-client need to wait for DB connection thread to get DB query result & notify him.

In this example, client establish a new connection with server application for every new query to the server. Server close the connection after sent the client the query result.

In this example, a server application’s TCP connection to DB that does not last more than 1 minute is considered an exceptional connection error. When exceptional connection errors count more than 10 times, reattempt to connect stop.

In this simulated employee clock-in & clock-out applications, multiple client applications are able to read or write clock-in or clock-out time concurrently from-or-to database via a single server application.
 
##
Revision ‘0’:

c# multithreaded server application with database connections pool Console App, both portal server & worker servers listen using TCP:   [ServerWithDB_multithreaded.cs.txt](https://github.com/lksark/Multithreaded-server-with-multiple-database-connections-pool/blob/main/ServerWithDB_multithreaded.cs.txt)

c# client querying DB Windows Forms App, only TCP:    [webDB_Client.zip](https://github.com/lksark/Multithreaded-server-with-multiple-database-connections-pool/blob/main/webDB_Client.zip)

MS SQL Server MDF database backup file:   [db_Employee_Attendance.bak](https://github.com/lksark/Multithreaded-server-with-multiple-database-connections-pool/blob/main/db_Employee_Attendance.bak)

##
Revision ‘1’:

1. Server & client applications use XML to send string & parse received string.
2. Add timeout check in client program. If server does not respond to client for extended period, client send ‘<CLIENT><command>IDLE TIMEOUT</command></CLIENT>’ message to server and close the connection.
3. Portal server listens using UDP, worker servers listen using TCP. Clients firstly connect to portal server using UDP, then connect to assigned worker server using TCP.

c# Console App - multithreaded server application with database connections pool. Portal server listens using UDP, worker servers listen using TCP:   [ServerWithDB_multithreaded_UDP-TCP.cs.txt](https://github.com/lksark/Multithreaded-server-with-multiple-database-connections-pool/blob/main/ServerWithDB_multithreaded_UDP-TCP.cs.txt)

c# client querying DB Windows Forms App, first UDP then TCP:    [webDB_Client_UDP-TCP.zip] (https://github.com/lksark/Multithreaded-server-with-multiple-database-connections-pool/blob/main/webDB_Client_UDP-TCP.zip)

Same MS SQL Server MDF database backup file:   [db_Employee_Attendance.bak](https://github.com/lksark/Multithreaded-server-with-multiple-database-connections-pool/blob/main/db_Employee_Attendance.bak)
