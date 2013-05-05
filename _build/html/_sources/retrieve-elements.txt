.. _retrieve-elements:

Retrieving elements from databases
""""""""""""""""""""""""""""""""""

The simplest way to retrieve elements from a database is the DB->get interface.

The DB->get interface takes the same five arguments that the DB->put interface takes:

db
    The database handle returned by db_create.

txnid
    A transaction ID. In our simple case, we aren't expecting to recover the database after application or system crash, so we aren't using transactions, and will leave this argument NULL.

key
    The key item for the key/data pair that we want to retrieve from the database.

data
    The data item for the key/data pair that we want to retrieve from the database.

flags
    Optional flags modifying the underlying behavior of the DB->get interface. 

Here's what the code to call DB->get looks like:
::
    #include <sys/types.h>
    #include <stdio.h>
    #include <db.h>
    #define DATABASE "access.db"
    int main() { 
    DB *dbp; DBT key, data; int ret;
    if ((ret = db_create(&dbp, NULL, 0)) != 0) { 
	fprintf(stderr, "db_create: %s\n", db_strerror(ret)); exit (1);
    } if ((ret = dbp->open(dbp, NULL, DATABASE, NULL, DB_BTREE, DB_CREATE, 0664)) != 0) { 
	dbp->err(dbp, ret, "%s", DATABASE); goto err; }
    memset(&key, 0, sizeof(key)); memset(&data, 0, sizeof(data)); 
    key.data = "fruit"; key.size = sizeof("fruit"); 
    data.data = "apple"; data.size = sizeof("apple");
    if ((ret = dbp->put(dbp, NULL, &key, &data, 0)) == 0) 
	printf("db: %s: key stored.\n", (char *)key.data);
    else { dbp->err(dbp, ret, "DB->put"); goto err; }
    if ((ret = dbp->get(dbp, NULL, &key, &data, 0)) == 0) 
	printf("db: %s: key retrieved: data was %s.\n", (char *)key.data, (char *)data.data); 
    else { dbp->err(dbp, ret, "DB->get"); goto err; } 

It is not usually necessary to clear the DBT structures passed to the Berkeley DB functions between calls. This is not always true, when some of the less commonly used flags for DBT structures are used. The DBT manual page specified the details of those cases.

It is possible, of course, to distinguish between system errors and the key/data pair simply not existing in the database. There are three standard returns from DB->get:

   1. The call might be successful and the key found, in which case the return value will be 0.
   2. The call might be successful, but the key not found, in which case the return value will be DB_NOTFOUND.
   3. The call might not be successful, in which case the return value will be a system error. 
