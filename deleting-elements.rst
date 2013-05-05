.. deleting-elements:
Deleting elements from Database
"""""""""""""""""""""""""""""""

The simplest way to remove elements from a database is the DB->del interface.

The DB->del interface takes four of the same five arguments that the DB->get and DB->put interfaces take. The difference is that there is no need to specify a data item, as the delete operation is only interested in the key that you want to remove.

db
    The database handle returned by db_create.

txnid
    A transaction ID. In our simple case, we aren't expecting to recover the database after application or system crash, so we aren't using transactions, and will leave this argument unspecified.

key
    The key item for the key/data pair that we want to delete from the database.

flags
    Optional flags modifying the underlying behavior of the DB->del interface. There are currently no available flags for this interface, so the flags argument should always be set to 0. 

Here's what the code to call DB->del looks like:
::
    #include <sys/types.h>
    #include <stdio.h>
    #include <db.h>
    #define DATABASE "access.db"
    int main() { 
	DB *dbp; DBT key, data; int ret;
	if ((ret = db_create(&dbp, NULL, 0)) != 0) { 
	   fprintf(stderr, "db_create: %s\n", db_strerror(ret)); exit (1); }
	if ((ret = dbp->open(dbp, NULL, DATABASE, NULL, DB_BTREE, DB_CREATE, 0664)) != 0) { 
	   dbp->err(dbp, ret, "%s", DATABASE); goto err; }
	memset(&key, 0, sizeof(key)); memset(&data, 0, sizeof(data)); 
	key.data = "fruit"; key.size = sizeof("fruit"); data.data = "apple"; data.size = sizeof("apple");
	if ((ret = dbp->put(dbp, NULL, &key, &data, 0)) == 0) 
	   printf("db: %s: key stored.\n", (char *)key.data); 
	else { dbp->err(dbp, ret, "DB->put"); goto err; }
	if ((ret = dbp->get(dbp, NULL, &key, &data, 0)) == 0) 
	   printf("db: %s: key retrieved: data was %s.\n", (char *)key.data, (char *)data.data); 
	else { dbp->err(dbp, ret, "DB->get"); goto err; }
	if ((ret = dbp->del(dbp, NULL, &key, 0)) == 0) 
	   printf("db: %s: key was deleted.\n", (char *)key.data); 
	else { dbp->err(dbp, ret, "DB->del"); goto err; } 

After the DB->del call returns, the entry to which the key fruit refers has been removed from the database. 
