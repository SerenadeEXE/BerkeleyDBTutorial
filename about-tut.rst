.. _about-tutorial:

*******************
About this Tutorial
*******************
For this tutorial you should have the C library accessable from the `#inport <db.h>` command.
If you have an `undefined reference to ...` then it is likely that you have a linking error.

Programming concepts
""""""""""""""""""""

The programming concepts are: 
    * Key/data pairs
    * Object handles
    * Error returns 

This database application will:
    * Create a simple database
    * Store items
    * Retrieve items
    * Remove items
    * Close the database 

The introduction will be presented using the programming language C. The complete source of the final version of the example program is included in the Berkeley DB distribution. 

key/data pairs
--------------

Berkeley DB uses key/data pairs to identify elements in the database. That is, in the general case, whenever you call a Berkeley DB interface, you present a key to identify the key/data pair on which you intend to operate.

For example, you might store some key/data pairs as follows:

=====  =======
Key:   Data:
=====  =======
fruit  apple
sport  cricket
drink  water
=====  =======

In each case, the first element of the pair is the key, and the second is the data. To store the first of these key/data pairs into the database, you would call the Berkeley DB interface to store items, with fruit as the key, and apple as the data. At some future time, you could then retrieve the data item associated with fruit, and the Berkeley DB retrieval interface would return apple to you. While there are many variations and some subtleties, all accesses to data in Berkeley DB come down to key/data pairs.

Both key and data items are stored in simple structures (called :ref:`DBT` ) that contain a reference to memory and a length, counted in bytes. (The name DBT is an acronym for database thang, chosen because nobody could think of a sensible name that wasn't already in use somewhere else.) Key and data items can be arbitrary binary data of practically any length, including 0 bytes. There is a single data item for each key item, by default, but databases can be configured to support multiple data items for each key item. 

