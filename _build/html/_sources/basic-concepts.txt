.. _basic-concepts:

Object Handles
""""""""""""""

With a few minor exceptions, Berkeley DB functionality is accessed by creating a structure and then calling functions that are fields in that structure. This is, of course, similar to object-oriented concepts, of instances and methods on them. For simplicity, we will often refer to these structure fields as methods of the handle.

The manual pages will show these methods as C structure references. For example, the open-a-database method for a database handle is represented as DB->open. 
::
	#include <db.h>
	int DB->open(DB *db, DB_TXN *txnid, const char *file, const char *database, DBTYPE type, u_int32_t flags, int mode);

Description
-----------

The DB->open interface opens the database represented by the file and database arguments for both reading and writing. The file argument is used as the name of an underlying file that will be used to back the database. The database argument is optional, and allows applications to have multiple databases in a single file. Although no database argument needs to be specified, it is an error to attempt to open a second database in a file that was not initially created using a database name. Further, the database argument is not supported by the Queue format. Finally, when opening multiple databases in the same physical file, it is important to consider locking and memory cache issues; see Opening multiple databases in a single file for more information.

In-memory databases never intended to be preserved on disk may be created by setting both the file and database arguments to NULL. Note that in-memory databases can only ever be shared by sharing the single database handle that created them, in circumstances where doing so is safe.

The type argument is of type DBTYPE, and must be set to one of DB_BTREE, DB_HASH, DB_QUEUE, DB_RECNO, or DB_UNKNOWN. If type is DB_UNKNOWN, the database must already exist and DB->open will automatically determine its type. The DB->get_type method may be used to determine the underlying type of databases opened using DB_UNKNOWN.

If the operation is to be transaction-protected (other than by specifying the DB_AUTO_COMMIT flag), the txnid parameter is a transaction handle returned from DB_ENV->txn_begin; otherwise, NULL.

The flags and mode arguments specify how files will be opened and/or created if they do not already exist.

The flags value must be set to 0 or by bitwise inclusively OR'ing together one or more of the following values:

DB_AUTO_COMMIT
    Enclose the DB->open call within a transaction. If the call succeeds, the open operation will be recoverable. If the call fails, no database will have been created.

DB_CREATE
    Create the database. If the database does not already exist and the DB_CREATE flag is not specified, the DB->open will fail.

DB_DIRTY_READ
    Support dirty reads; that is, read operations on the database may request the return of modified but not yet committed data.

DB_EXCL
    Return an error if the database already exists. The DB_EXCL flag is only meaningful when specified with the DB_CREATE flag.

DB_NOMMAP
    Do not map this database into process memory (see the description of the DB_ENV->set_mp_mmapsize method for further information).

DB_RDONLY
    Open the database for reading only. Any attempt to modify items in the database will fail, regardless of the actual permissions of any underlying files.

DB_THREAD
    Cause the DB handle returned by DB->open to be free-threaded; that is, usable by multiple threads within a single address space.

DB_TRUNCATE
    Physically truncate the underlying file, discarding all previous databases it might have held. Underlying filesystem primitives are used to implement this flag. For this reason, it is applicable only to the file and cannot be used to discard databases within a file.

    The DB_TRUNCATE flag cannot be transaction-protected, and it is an error to specify it in a transaction-protected environment. 

On UNIX systems or in IEEE/ANSI Std 1003.1 (POSIX) environments, all files created by the database open are created with mode mode (as described in chmod(2)) and modified by the process' umask value at the time of creation (see umask(2)). If mode is 0, the database open will use a default mode of readable and writable by both owner and group. On Windows systems, the mode argument is ignored. The group ownership of created files is based on the system and directory defaults, and is not further specified by Berkeley DB.

Calling DB->open is a reasonably expensive operation, and maintaining a set of open databases will normally be preferable to repeatedly opening and closing the database for each new query.

The DB->open method returns a non-zero error value on failure and 0 on success. If DB->open fails, the DB->close method should be called to discard the DB handle.

Environment Variables
---------------------

DB_HOME
    If a dbenv argument to db_create was specified, the environment variable DB_HOME may be used as the path of the database environment home.

    DB->open is affected by any database directory specified using the DB_ENV->set_data_dir method, or by setting the "set_data_dir" string in the environment's DB_CONFIG file. 

TMPDIR
    If the file and dbenv arguments to DB->open are NULL, the environment variable TMPDIR may be used as a directory in which to create temporary backing files 

Errors
------

The DB->open method may fail and return a non-zero error for the following conditions:

DB_LOCK_DEADLOCK
    The operation was selected to resolve a deadlock. 

DB_OLD_VERSION
    The database cannot be opened without being first upgraded.

EEXIST
    DB_CREATE and DB_EXCL were specified and the database exists.

EINVAL
    An invalid flag value or parameter was specified. (For example, unknown database type, page size, hash function, pad byte, byte order) or a flag value or parameter that is incompatible with the specified database.

    The DB_THREAD flag was specified and fast mutexes are not available for this architecture.

    The DB_THREAD flag was specified to DB->open, but was not specified to the DB_ENV->open call for the environment in which the DB handle was created.

    A backing flat text file was specified with either the DB_THREAD flag or the provided database environment supports transaction processing.

ENOENT
    A nonexistent re_source file was specified. 

The DB->open method may fail and return a non-zero error for errors specified for other Berkeley DB and C library or system functions. If a catastrophic error has occurred, the DB->open method may fail and return DB_RUNRECOVERY, in which case all subsequent Berkeley DB calls will fail in the same way. 
