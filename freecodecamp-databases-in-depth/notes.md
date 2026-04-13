# Databases In-Depth – Complete Course

https://www.youtube.com/watch?v=pPqazMTzNOM

Index

1. Client and Network Layer
2. Tokeniser

---

## #1 Client and Network Layer

Every database has Client-Server Architecture.

Client is like front-end of the Database system.
It is program/software that allows us to request to the Database Server.

Server is like back-end of the Database system.
Server has a "Network Layer", which provides an interface to communicate with the Database Server.

Usually Databases have a seperate server except "SQLite".

**SQLite**  

SQLite is the most common and most simplest database for small to medium devices.

You don't need a seperate server for SQLite.
It is "Embeddable" - that means you don't need a seperate process for it,  
it is going to be embedded in the same process as the rest of the system.
So SQLite doesn't need a network layer.
But rest of the databases need a network layer.

---

## #2 Front-end = Tokeniser + Parser + Optimizer

**Tokeniser**  

Tokenizing the query  
Input query/command -> tokens  
It divides input query or command into tokens.

**Parser**

Parses the query/command -> is it a valid instruction/query/command? Does it make sense?

**Optimizer**

Before executing the query, datbase needs to understand what would be the best way  
of executing this query.
For that purpose most of the databases have an optimizer.

> Front-end = Tokeniser + Parser + Optimizer
> Tokeniser, Parser and Optimizer together form the front-end component of the database.

Front-end component of the database is responsible for making sense of  
whatever query is coming and converting it into something that the rest of  
the database or back-end components together can understand and execute.

