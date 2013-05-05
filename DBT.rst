.. _DBT:
.. _index:: DBT
DBT
"""

Storage and retrieval for the Berkeley DB access methods are based on key/data pairs. Both key and data items are represented by the DBT data structure. (The name DBT is a mnemonic for data base thang, and was used because no one could think of a reasonable name that wasn't already in use somewhere else.) Key and data byte strings may refer to strings of zero length up to strings of essentially unlimited length. See Database limits for more information.
::
    typedef struct {
    	void *data;
    	u_int32_t size;
    	u_int32_t ulen;
    	u_int32_t dlen;
    	u_int32_t doff;
    	u_int32_t flags;
    } DBT;

In order to ensure compatibility with future releases of Berkeley DB, all fields of the DBT structure that are not explicitly set should be initialized to nul bytes before the first time the structure is used. Do this by declaring the structure external or static, or by calling the C library routine bzero(3) or memset(3).

By default, the flags structure element is expected to be set to 0. In this default case, when the application is providing Berkeley DB a key or data item to store into the database, Berkeley DB expects the data structure element to point to a byte string of size bytes. When returning a key/data item to the application, Berkeley DB will store into the data structure element a pointer to a byte string of size bytes, and the memory to which the pointer refers will be allocated and managed by Berkeley DB.

The elements of the DBT structure are defined as follows:
.. index:: `void \*data;`, u_int32_t size;, u_int32_t ulen;,u_int32_t dlen;, u_int32_t doff;, u_int32_t flags;, DB_DBT_MALLOC, DB_DBT_REALLOC, DB_DBT_USERMEM, DB_DBT_PARTIAL

void \*data;
    A pointer to a byte string.

u_int32_t size;
    The length of data, in bytes.

u_int32_t ulen;
    The size of the user's buffer (to which data refers), in bytes. This location is not written by the Berkeley DB functions.

    Note that applications can determine the length of a record by setting the ulen field to 0 and checking the return value in the size field. See the DB_DBT_USERMEM flag for more information.

u_int32_t dlen;
    The length of the partial record being read or written by the application, in bytes. See the DB_DBT_PARTIAL flag for more information.

u_int32_t doff;
    The offset of the partial record being read or written by the application, in bytes. See the DB_DBT_PARTIAL flag for more information.

u_int32_t flags;

    The flags value must be set to 0 or by bitwise inclusively OR'ing together one or more of the following values:

    DB_DBT_MALLOC
        When this flag is set, Berkeley DB will allocate memory for the returned key or data item (using malloc(3), or the user-specified malloc function), and return a pointer to it in the data field of the key or data DBT structure. Because any allocated memory becomes the responsibility of the calling application, the caller must determine whether memory was allocated using the returned value of the data field.

        It is an error to specify more than one of DB_DBT_MALLOC, DB_DBT_REALLOC, and DB_DBT_USERMEM.

    DB_DBT_REALLOC
        When this flag is set Berkeley DB will allocate memory for the returned key or data item (using realloc(3), or the user-specified realloc function), and return a pointer to it in the data field of the key or data DBT structure. Because any allocated memory becomes the responsibility of the calling application, the caller must determine whether memory was allocated using the returned value of the data field.

        The difference between DB_DBT_MALLOC and DB_DBT_REALLOC is that the latter will call realloc(3) instead of malloc(3), so the allocated memory will be grown as necessary instead of the application doing repeated free/malloc calls.

        It is an error to specify more than one of DB_DBT_MALLOC, DB_DBT_REALLOC, and DB_DBT_USERMEM.

    DB_DBT_USERMEM
        The data field of the key or data structure must refer to memory that is at least ulen bytes in length. If the length of the requested item is less than or equal to that number of bytes, the item is copied into the memory to which the data field refers. Otherwise, the size field is set to the length needed for the requested item, and the error ENOMEM is returned.

        It is an error to specify more than one of DB_DBT_MALLOC, DB_DBT_REALLOC, and DB_DBT_USERMEM.

    DB_DBT_PARTIAL
        Do partial retrieval or storage of an item. If the calling application is doing a get, the dlen bytes starting doff bytes from the beginning of the retrieved data record are returned as if they comprised the entire record. If any or all of the specified bytes do not exist in the record, the get is successful, and any existing bytes are returned.

        For example, if the data portion of a retrieved record was 100 bytes, and a partial retrieval was done using a DBT having a dlen field of 20 and a doff field of 85, the get call would succeed, the data field would refer to the last 15 bytes of the record, and the size field would be set to 15.

        If the calling application is doing a put, the dlen bytes starting doff bytes from the beginning of the specified key's data record are replaced by the data specified by the data and size structure elements. If dlen is smaller than size, the record will grow; if dlen is larger than size, the record will shrink. If the specified bytes do not exist, the record will be extended using nul bytes as necessary, and the put call will succeed.

        It is an error to attempt a partial put using the DB->put function in a database that supports duplicate records. Partial puts in databases supporting duplicate records must be done using a DBcursor->c_put function.

        It is an error to attempt a partial put with differing dlen and size values in Queue or Recno databases with fixed-length records.

        For example, if the data portion of a retrieved record was 100 bytes, and a partial put was done using a DBT having a dlen field of 20, a doff field of 85, and a size field of 30, the resulting record would be 115 bytes in length, where the last 30 bytes would be those specified by the put call. 


