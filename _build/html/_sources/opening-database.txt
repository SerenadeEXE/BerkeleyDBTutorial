.. opening-database:

Opening a database
""""""""""""""""""

Opening a database is done in two steps: first, a DB handle is created using the Berkeley DB db_create interface, and then the actual database is opened using the DB->open function.

The db_create interface takes three arguments:

dbp
    A location to store a reference to the created structure.

environment
    A location to specify an enclosing Berkeley DB environment, not used in our example.

flags
    A placeholder for flags, not used in our example. 

The DB->open interface takes five arguments:

file
    The name of the database file to be opened.

database
    The optional database name, not used in this example.

type
    The type of database to open. This value will be one of the four access methods Berkeley DB supports: DB_BTREE, DB_HASH, DB_QUEUE or DB_RECNO, or the special value DB_UNKNOWN, which allows you to open an existing file without knowing its type.

flags
    Various flags that modify the behavior of DB->open. In our simple case, the only interesting flag is DB_CREATE. This flag behaves similarly to the IEEE/ANSI Std 1003.1 (POSIX) O_CREATE flag to the open system call, causing Berkeley DB to create the underlying database if it does not yet exist.

mode
    The file mode of any underlying files that DB->open will create. The mode behaves as does the IEEE/ANSI Std 1003.1 (POSIX) mode argument to the open system call, and specifies file read, write and execute permissions. Of course, only the read and write permissions are relevant to Berkeley DB. 

Here's what the code to create the handle and then call DB->open looks like:
::
    #include <sys/types.h>
    #include <stdio.h>
    #include <db.h>
    #define DATABASE "access.db"
    int main() { DB *dbp; int ret;
    if ((ret = db_create(&dbp, NULL, 0)) != 0) { 
	fprintf(stderr, "db_create: %s\n", db_strerror(ret)); exit (1);
    } if ((ret = dbp->open(dbp, NULL, DATABASE, NULL, DB_BTREE, DB_CREATE, 0664)) != 0) {
	dbp->err(dbp, ret, "%s", DATABASE); goto err;
    } 

If the call to db_create is successful, the variable dbp will contain a database handle that will be used to configure and access an underlying database.

As you see, the program opens a database named access.db. The underlying database is a Btree. Because the DB_CREATE flag was specified, the file will be created if it does not already exist. The mode of any created files will be 0664 (that is, readable and writable by the owner and the group, and readable by everyone else).

One additional function call is used in this code sample, DB->err. This method works like the ANSI C printf interface. The second argument is the error return from a Berkeley DB function, and the rest of the arguments are a printf-style format string and argument list. The error message associated with the error return will be appended to a message constructed from the format string and other arguments. In the above code, if the DB->open call were to fail, the message it would display would be something like

    access.db: Operation not permitted
