.. _adding-elements:

Adding items to database
""""""""""""""""""""""""
he simplest way to add elements to a database is the DB->put interface.

The DB->put interface takes five arguments:

db
    The database handle returned by db_create.

txnid
    A transaction handle. In our simple case, we aren't expecting to recover the database after application or system crash, so we aren't using transactions, and will leave this argument NULL.

key
    The key item for the key/data pair that we want to add to the database.

data
    The data item for the key/data pair that we want to add to the database.

flags
    Optional flags modifying the underlying behavior of the DB->put interface. 

Here's what the code to call DB->put looks like:
::
    #include <sys/types.h>
    #include <stdio.h>
    #include <db.h>

    #define DATABASE "access.db"

    int main() { DB *dbp; DBT key, data; int ret;
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

The first thing to notice about this new code is that we clear the DBT structures that we're about to pass as arguments to Berkeley DB functions. This is very important, and being careful to do so will result in fewer errors in your programs. All Berkeley DB structures instantiated in the application and handed to Berkeley DB should be cleared before use, without exception. This is necessary so that future versions of Berkeley DB may add additional fields to the structures. If applications clear the structures before use, it will be possible for Berkeley DB to change those structures without requiring that the applications be rewritten to be aware of the changes.

Notice also that we're storing the trailing nul byte found in the C strings "fruit" and "apple" in both the key and data items, that is, the trailing nul byte is part of the stored key, and therefore has to be specified in order to access the data item. There is no requirement to store the trailing nul byte, it simply makes it easier for us to display strings that we've stored in programming languages that use nul bytes to terminate strings.

In many applications, it is important not to overwrite existing data. For example, we might not want to store the key/data pair fruit/apple if it already existed, for example, if the key/data pair fruit/cherry had been previously stored into the database.

This is easily accomplished by adding the DB_NOOVERWRITE flag to the DB->put call:
::
    if ((ret =	dbp->put(dbp, NULL, &key, &data, DB_NOOVERWRITE)) == 0)
    	printf("db: %s: key stored.\n", (char *)key.data);
    else {
    	dbp->err(dbp, ret, "DB->put");
    	goto err;
    }

This flag causes the underlying database functions to not overwrite any previously existing key/data pair. (Note that the value of the previously existing data doesn't matter in this case. The only question is if a key/data pair already exists where the key matches the key that we are trying to store.)

Specifying DB_NOOVERWRITE opens up the possibility of a new Berkeley DB return value from the DB->put function, DB_KEYEXIST, which means we were unable to add the key/data pair to the database because the key already existed in the database. While the above sample code simply displays a message in this case:

    DB->put: DB_KEYEXIST: Key/data pair already exists

The following code shows an explicit check for this possibility:
::
    switch (ret = dbp->put(dbp, NULL, &key, &data, DB_NOOVERWRITE)) {
    case 0:
    	printf("db: %s: key stored.\n", (char *)key.data);
    	break;
    case DB_KEYEXIST:
    	printf("db: %s: key previously stored.\n",
    	(char *)key.data);
    	break;
    default:
    	dbp->err(dbp, ret, "DB->put");
    	goto err;
    }
