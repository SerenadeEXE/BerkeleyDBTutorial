.. _introduction:

Introduction
============
This information was originally in http://linuxfromsratch.org/blfs/view/svn/server/db.html

There was some minor modifications to make it more general, ie include other forms of distributions besides 5.3.21.

The Berkeley DB package contains programs and utilities used by many other applications for database related functions.

We willl use DIST=db-5.3.21, but here you can substitute your own distrubutions.This package is known to build and work properly using an LFS-7.3 platform.
Package Information

|	Download (HTTP): http://download.oracle.com/berkeley-db/DIST.tar.gz
|	Download MD5 sum: 3fda0b004acdaa6fa350bfc41a3b95ca
|	Download size: 34 MB
|	Estimated disk space required: 265 MB
|	Estimated build time: 0.4 SBU

User Notes: http://wiki.linuxfromscratch.org/blfs/wiki/db

Installation of Berkeley DB
---------------------------

Install Berkeley DB by running the following commands:
::
	cd build_unix &&
	../dist/configure --prefix=/usr --enable-compat185 --enable-dbm --disable-static --enable-cxx       &&
	make

Now, as the root user:
::
	make docdir=/usr/share/doc/DIST install &&
	chown -v -R root:root /usr/bin/db_* /usr/include/db{,_185,_cxx}.h /usr/lib/libdb*.{so,la} /usr/share/doc/DIST

Command Explanations
--------------------

cd build_unix && ../dist/configure --prefix=/usr...: This replaces the normal ./configure command, as Berkeley DB comes with various build directories for different platforms.

--enable-compat185:	This switch enables building the DB-1.85 compatibility API.

--enable-cxx:		This switch enables building C++ API libraries.

--enable-dbm:		Enables legacy interface support needed by some older packages.

make docdir=/usr/share/doc/DIST install: This installs the documentation in the standard location instead of /usr/docs.

chown -v -R root:root ...:This command changes the ownership of various installed files from the uid:gid of the builder to root:root.

--enable-tcl --with-tcl=/usr/lib:	Enables Tcl support in DB and creates the libdb_tcl libraries.

--enable-java:				Enables Java support in DB and creates the libdb_java libraries.

Contents
--------

Installed Programs: db_archive, db_checkpoint, db_deadlock, db_dump, db_hotbackup, db_load, db_log_verify, db_printlog, db_recover, db_replicate, db_stat, db_tuner, db_upgrade and db_verify.
Installed Libraries: libdb.so and libdb_cxx.so
Installed Directory: /usr/share/doc/db-5.3.21

Short Descriptions
^^^^^^^^^^^^^^^^^^
.. index:: db_archive, db_checkpoint, db_deadlock, db_dump, db_hotbackup, db_load, db_log_verify, db_printlog, db_recover, db_replicate, db_stat, db_tuner, db_upgrade, db_verify

db_archive

	prints the pathnames of log files that are no longer in use.

db_checkpoint

	is a daemon process used to monitor and checkpoint database logs.

db_deadlock

	is used to abort lock requests when deadlocks are detected.

db_dump

	converts database files to a flat file format readable by db_load.

db_hotbackup

	creates "hot backup" or "hot failover" snapshots of Berkeley DB databases.

db_load

	is used to create database files from flat files created with db_dump.

db_log_verify

	verifies the log files of a database.

db_printlog

	converts database log files to human readable text.

db_recover

	is used to restore a database to a consistent state after a failure.

db_replicate

	is a daemon process that provides replication/HA services on a transactional environment.

db_stat

	displays database environment statistics

db_tuner

	analyzes the data in a btree database, and suggests a page size that is likely to deliver optimal operation.

db_upgrade

	is used to upgrade database files to a newer version of Berkeley DB.

db_verify

	is used to run consistency checks on database files.

