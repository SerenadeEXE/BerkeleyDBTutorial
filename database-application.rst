.. _database-application:

Simple Access example
"""""""""""""""""""""

By now you should have an ituition on how Berkeley DB access methods works. We show a simple example using what we have learned so far.
::
	int
	main(argc, argv)
		int argc;
		char *argv[];
	{
		extern int optind;
		DB *dbp;
		DBC *dbcp;
		DBT key, data;
		size_t len;
		int ch, ret, rflag;
		char *database, *p, *t, buf[1024], rbuf[1024];
		const char *progname = "ex_access";		/* Program name. */

We will build a program which takes in strings as keys and thier data content is the reverse of the string for example. If we provide a key `apple` the data would be `elppa`.
Above is what we will need for the program, note here we use DBC which is a cursor, an itterator, to iterate through the data when we print out the infromation. S
We omit the input processing to keep this example short.
First we need to create and open the database, here we are using a Btree for our database type.
::
	...
	/* Create and initialize database object, open the database. */
	if ((ret = db_create(&dbp, NULL, 0)) != 0) {
		fprintf(stderr,
		    "%s: db_create: %s\n", progname, db_strerror(ret));
		return (EXIT_FAILURE);
	}
	...
	if ((ret = dbp->open(dbp,
	    NULL, database, NULL, DB_BTREE, DB_CREATE, 0664)) != 0) {
		dbp->err(dbp, ret, "%s: open", database);
		goto err1;
	}

We first need to fill the database with infromation until the user specifies `exit` or `quit`. Note that we use NOOVERWRITE since will be adding the same element.
::
	memset(&key, 0, sizeof(DBT));
	memset(&data, 0, sizeof(DBT));
	for (;;) {
		printf("input> ");
		fflush(stdout);
		if (fgets(buf, sizeof(buf), stdin) == NULL)
			break;
		if (strcmp(buf, "exit\n") == 0 || strcmp(buf, "quit\n") == 0)
			break;
		if ((len = strlen(buf)) <= 1)
			continue;
		for (t = rbuf, p = buf + (len - 2); p >= buf;)
			*t++ = *p--;
		*t++ = '\0';

		key.data = buf;
		data.data = rbuf;
		data.size = key.size = (u_int32_t)len - 1;

		switch (ret =
		    dbp->put(dbp, NULL, &key, &data, DB_NOOVERWRITE)) {
		case 0:
			break;
		default:
			dbp->err(dbp, ret, "DB->put");
			if (ret != DB_KEYEXIST)
				goto err1;
			break;
		}
	}

Finally we want to walk throught the database to look at the items which we have added and close the database. The dbcp is a handle to the records in the database thus we can access the next, previous and other methods to itterate through the database.
::
	...
	/* Initialize the key/data pair so the flags aren't set. */
	memset(&key, 0, sizeof(key));
	memset(&data, 0, sizeof(data));

	/* Walk through the database and print out the key/data pairs. */
	while ((ret = dbcp->get(dbcp, &key, &data, DB_NEXT)) == 0)
		printf("%.*s : %.*s\n",
		    (int)key.size, (char *)key.data,
		    (int)data.size, (char *)data.data);
	if (ret != DB_NOTFOUND) {
		dbp->err(dbp, ret, "DBcursor->get");
		goto err2;
	}

	/* Close everything down. */
	if ((ret = dbcp->close(dbcp)) != 0) {
		dbp->err(dbp, ret, "DBcursor->close");
		goto err1;
	}
	if ((ret = dbp->close(dbp, 0)) != 0) {
		fprintf(stderr,
		    "%s: DB->close: %s\n", progname, db_strerror(ret));
		return (EXIT_FAILURE);

To compile this program we would use
::
	gcc ex_access.c -ldb

where the `-ldb` indicates that we are linking the Berkeley DB library.


