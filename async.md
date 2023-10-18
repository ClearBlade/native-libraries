The ClearBlade Async library works with the event loop to provide asynchronous ClearBlade functions.
The `init` function is not required.

__Reference__
1. [Query](#query)
2. [Collections](#collections)
3. [Databases](#databases)
4. [File management](#file-management)
5. [Locks](#locks)
6. [Users](#users)
7. [Devices](#devices)
8. [Auth](#auth)
9. [Roles/Perms](#rolesperms)
10. [Caches](#caches)
11. [Triggers](#triggers)
12. [Timers](#timers)
13. [Edges](#edges)
14. [Adapters](#adapters)
15. [Collection custom sync](#collection-custom-sync)
16. [Secret](#secret)
17. [Preloader](#preloader)
18. [GoogleCloudLogger](#google-cloud-logger)
19. [Data usage](#data-usage)
20. [GoogleCloudMonitoring](#google-cloud-monitoring)

__Examples__
1. [Collections](#collection-examples)
2. [Databases](#database-examples)
3. [File management](#file-management-examples)
4. [Adapter examples](#adapter-examples)
5. [Preloader examples](#preloader-examples)

# Reference

## Query

Note: by default, all queries are limited to 100 results. To change this limit, use the `setPage` method.

~~~javascript
/**
 * Represents a query.
 * @returns {Query}
 */
ClearBladeAsync.Query()

/**
 * Modifies a query in place, ANDing the given filter.
 * @param {string} column
 * @param {*} value
 * @returns {Query} with the filter applied
 */
Query.equalTo(column, value)
Query.greaterThan(column, value)
Query.greaterThanEqualTo(column, value)
Query.lessThan(column, value)
Query.lessThanEqualTo(column, value)
Query.notEqualTo(column, value)
Query.matches(column, value)

/**
 * Specifies limits on the query return size.
 * @param {number} pageSize: The max number of rows to return
 * @param {number} pageNum: The number of pages to offset return data (1 = first page)
 * @returns {Query} with limits applied
 */
Query.setPage(pageSize, pageNum)

/**
 * Specifies how the returned data should be sorted.
 * @param {string} column: The column name which should be used to sort
 * @returns {Query} with sorting applied
 */
Query.ascending(column)
Query.descending(column)

/**
 * Limits the data returned to only include the given columns.
 * @param {string[]} columns: The list of column names
 * @returns {Query} with filter applied
 */
Query.columns(columns)

/**
 * Sets a query string to be used in certain MongoDB operations.
 * @param {string} query
 * @returns {Query} with query applied
 */
Query.rawQuery(query)

/**
 * Combines two queries with boolean OR condition.
 * @param {Query} query
 * @returns {Query}
 */
Query.or(query)
~~~

## Collections

[Examples](#collection-examples)

~~~javascript
/**
 * Creates a new ClearBlade collection.
 * Promise resolves with an object containing the new collection's metadata.
 * @param {string} name
 * @returns {Promise<{name: string, id: string}>}
 */
ClearBladeAsync.newCollection(name)

/**
 * Represents a ClearBlade collection.
 * @param {string} nameOrID
 * @returns {Collection}
 */
ClearBladeAsync.Collection(nameOrID)

/**
 * @typedef {Object} fetchResponse
 * @property {Object[]} DATA: Requested collection rows
 * @property {number} TOTAL: The number of objects in the DATA field
 * @property {number} CURRENTPAGE
 * @property {string} NEXTPAGEURL
 * @property {string} PREVPAGEURL
 */

/**
 * Gets data from the collection.
 * Promise resolves with the requested collection rows.
 * @param {Query} [query]
 * @returns {Promise<fetchResponse>}
 */
Collection.fetch(query)

/**
 * Creates a row in the collection.
 * Promise resolves with a list of item_id's for the newly created rows.
 * @param {Object} newItem
 * @returns {Promise<string[]>}
 */
Collection.create(newItem)

/**
 * Updates rows in the collection.
 * Promise resolves with the updated rows.
 * @param {Query} [query]
 * @param {Object} changes
 * @returns {Promise<Object[]>}
 */
Collection.update(query, changes)

/**
 * If the given item does not exist in the collection, it will be inserted.
 * If the item does exist, it will be updated.
 * Existence is determined by the conflictColumn, which must be a primary key or have a unique index.
 * Promise resolves with the updated rows.
 * @param {Object} item
 * @param {string} conflictColumn
 * @returns {Promise<Object[]>}
 */
Collection.upsert(item, conflictColumn)

/**
 * Deletes rows from the collection.
 * Promise resolves empty.
 * @param {Query} [query]
 * @returns {Promise<>}
 */
Collection.remove(query)

/**
 * Counts rows in the collection.
 * Promise resolves with the number of collection rows matching the provided query.
 * @param {Query} [query]
 * @returns {Promise<{count: number}>}
 */
Collection.count(query)

/**
 * Gets all column metadata for the collection.
 * Promise resolves with the list of column metadata.
 * @returns {Promise<{ColumnName: string, ColumnType: string, PK: boolean}[]>}
 */
Collection.columns()

/**
 * Adds a column to the collection.
 * Promise resolves empty.
 * @param {{name: string, type: string}} columnMeta
 * @returns {Promise<>}
 */
Collection.addColumn(columnMeta)

/**
 * Removes a column from the collection.
 * Promise resolves empty.
 * @param {string} columnName
 * @returns {Promise<>}
 */
Collection.dropColumn(columnName)

/**
 * Creates an index on the given column.
 * An index will speed up queries referencing the indexed column,
 * but will require more space to store your data.
 * By default, the item_id columns of all collections are indexed.
 * Promise resolves empty.
 * @param {string} columnName
 * @returns {Promise<>}
 */
Collection.createIndex(columnName)

/**
 * Creates a unique index on the given column.
 * A unique index provides the same speed benefit as a regular index,
 * and enforces each entry if the indexed column is unique.
 * Promise resolves empty.
 * @param {string} columnName
 * @returns {Promise<>}
 */
Collection.createUniqueIndex(columnName)

/**
 * Deletes the collection and all data.
 * Promise resolves empty.
 * @returns {Promise<>}
 */
Collection.deleteCollection()
~~~

## Databases

[Examples](#database-examples)

~~~javascript
/**
 * Represents a database.
 * If options specify externalDBName, the database represents the external database.
 * If options are empty, the database represents the ClearBlade system's database.
 * @param {{externalDBName: string}} options
 * @returns {Database}
 */
ClearBladeAsync.Database(options)

/**
 * Performs a SQL query on ClearBlade collections.
 * Promise resolves with the requested database rows.
 * Note: A raw query will not fire triggers or sync your changes.
 * @param {string} rawQuery
 * @param {...*} [params] the query's optional parameters
 * @returns {Promise<Object[]>}
 */
Database.query(rawQuery, params)

/**
 * Executes a SQL operation on ClearBlade collections.
 * Promise resolves with the count of rows affected.
 * No database rows are returned with this function.
 * Note: A raw query will not fire triggers or sync your changes.
 * @param {string} rawQuery
 * @param {...*} [params] the query's optional parameters
 * @returns {Promise<{count: number}>}
 */
Database.exec(rawQuery, params)

/**
 * Performs a raw operation on an external database.
 * Promise resolves with the database operation's result.
 * @param {string} operation
 * @param {...*} [args] optional arguments to the operation
 * @returns {Promise<*>}
 */
Database.performOperation(operation, args)

/**
 * @typedef {Object} Statement
 * @prop {string} statement: A SQL statement
 * @prop {[]any} args: The statement's parameters
 */

/**
 * Helper function to group statements with their queries for transactions.
 * @param {string} statement: A SQL statement
 * @param {...*} [args]: The statement's parameters
 * @returns {Statement}
 */
Database.statement(statement[, args])

/**
 * Executes a SQL transaction on a ClearBlade collection with the given statements.
 * If one of the statements throws an error, the entire transaction is rolled back.
 * All statements are treated the same as exec. No results will be returned.
 * Promise resolves empty on success.
 * @param {[]Statement} statements
 * @returns {Promise<>}
 */
Database.transaction(statements)
~~~

## File management

Note: All file paths are relative to the bucket set root, except on an edge where you can use full paths.

[Examples](#file-management-examples)

~~~javascript
/**
 * Represents a synced filesystem.
 *
 * @param {string} deployment_name or bucket set name
 *
 * @returns {FS}
 */
ClearBladeAsync.FS(deployment_name)

/**
 * Recursively reads a directory's contents.
 * The promise is resolved with an array of file names in 
 * that directory and all directories below it.
 *
 * @param {string} path
 *
 * @returns {Promise<string[]>}
 */
FS.readDir(path)

/**
 * Reads the entire contents of a file.
 * The promise is resolved with the file contents.
 * If encoding is not specified, the contents will be a Uint8Array.
 * If encoding is specified, the contents will be a string.
 *
 * @param {string} path: The relative (full if on edge) path with a filename with an extension.
 * @param {string} [encoding]
 *
 * @returns {Promise<string|UInt8Array>}
 */
FS.readFile(path[, encoding])

/**
 * Writes the given data to a file, replacing it if it already exists.
 * The promise is resolved empty.
 *
 * @param {string} path: The relative (full if on edge) path with a filename with an extension.
 * @param {string|Uint8Array} data
 *
 * @returns {Promise<>}
 */
FS.writeFile(path, data)

/**
 * Renames (mv's) oldPath to newPath.
 * The promise is resolved empty.
 *
 * @param {string} oldPath: The relative (full if on edge) path with a filename with an extension.
 * @param {string} newPath: The relative (full if on edge) path with a filename with an extension.
 *
 * @returns {Promise<>}
 */
FS.renameFile(oldPath, newPath)

/**
 * Copies srcPath to dstPath, overwriting dstPath if it already exists.
 * The promise is resolved empty.
 *
 * @param {string} srcPath: The relative (full if on edge) path with a filename with an extension.
 * @param {string} dstPath: The relative (full if on edge) path with a filename with an extension.
 *
 * @returns {Promise<>}
 */
FS.copyFile(srcPath, dstPath)

/**
 * Deletes the file at the specified path.
 * The promise is resolved empty.
 *
 * @param {string} path: The relative (full if on edge) path with a filename with an extension.
 *
 * @returns {Promise<>}
 */
FS.deleteFile(path)

/**
 * @typedef {Object} FileStats
 * Represents file metadata.
 *
 * @property {number} size: The file size in bytes
 * @property {string} permissions: Ex: "-rwxrwxrwx"
 *
 */

/**
 * Syncs path (on the platform) to the specified edge's inbox
 * The promise is resolved empty.
 *
 * @param {string} path: The relative path (in platform bucket set) with a filename with an extension.
 * @param {string} edge: The edge's name to sync with. The path will end up in that edge's inbox
 *
 * @returns {Promise<>}
 */
FS.syncFileToEdge(path, edge)

/**
 * Retrieves metadata for the given file path.
 *
 * @param {string} path: The relative (full if on edge) path with a filename with an extension.
 *
 * @returns {Promise<FileStats>}
 */
FS.stat(path)

/**
 * Represents a filesystem's file.
 * Useful if you're doing multiple operations on a single file.
 *
 * @param {string} deployment_name or bucket set name
 * @param {string} path: The relative (full if on edge) path with a filename with an extension.
 *
 * @returns {File}
 */
ClearBladeAsync.File(deployment_name, path)

/**
 * Retrieves metadata for the file.
 *
 * @returns {Promise<FileStats>}
 */
File.stat()

/**
 * Reads the file's entire contents (where the promise is resolved).
 * If encoding is not specified, contents will be a Uint8Array.
 * If encoding is specified, contents will be a string.
 *
 * @param {string} [encoding]
 *
 * @returns {Promise<string|UInt8Array>}
 */
File.read([encoding])

/**
 * Writes the given data to the file, replacing the contents if it already exists.
 * The promise is resolved empty.
 *
 * @param {string|Uint8Array} data
 *
 * @returns {Promise<>}
 */
File.write(data)

/**
 * Renames (mv's) file to newPath.
 * The promise is resolved empty.
 *
 * @param {string} newPath: The relative (full if on edge) path with a filename with an extension
 *
 * @returns {Promise<>}
 */
File.rename(newPath)

/**
 * Copies file to dstPath, overwriting dstPath if it already exists.
 * The promise is resolved empty.
 *
 * @param {string} dstPath: The relative (full if on edge) path with a filename with an extension
 *
 * @returns {Promise<>}
 */
File.copy(dstPath)

/**
 * Deletes the file.
 * The promise is resolved empty.
 *
 * @returns {Promise<>}
 */
File.delete()
~~~

## Locks

~~~javascript
/**
 * Represents a lock
 * @param {string} name
 * @param {string} caller
 * @returns {Lock}
 */
ClearBladeAsync.Lock(name, caller)

/**
 * Obtains an exclusive lock.
 * Promise will resolve when the lock has been granted.
 * @returns {Promise<>}
 */
Lock.lock()

/**
 * Releases an exclusive lock.
 * Promise will resolve when the lock has been released.
 * @returns {Promise<>}
 */
Lock.unlock()

/**
 * Obtains a shared lock for read access.
 * Promise will resolve when the lock has been granted.
 * @returns {Promise<>}
 */
Lock.rlock()

/**
 * Releases a shared lock.
 * Promise will resolve when the lock has been released.
 * @returns {Promise<>}
 */
Lock.runlock()
~~~

## Users

~~~javascript
/**
 * Represents the users table.
 * @returns {Users}
 */
ClearBladeAsync.Users()

/**
 * @typedef {Object} UserInfo
 * @property {string} email
 * @property {string} password
 */

/**
 * Creates a new user in the user table.
 * Promise will resolve with the user table row of the new user.
 * @param {UserInfo} info: The initial data describing the new user.
 * @returns {Promise<string>}: The user_id for the newly created user
 */
Users.create(info)

/**
 * Fetches rows from the user table.
 * Promise will resolve with the user table rows matching the provided query.
 * @param {Query} query
 * @returns {Promise<Object[]>}
 */
Users.read(query)

/**
 * Updates rows in the user table.
 * Promise will resolve empty.
 * @param {Query} query
 * @param {Object} changes
 * @returns {Promise<>}
 */
Users.update(query, changes)

/**
 * Deletes rows from the user table.
 * Promise will resolve empty.
 * @param {Query} query
 * @returns {Promise<>}
 */
Users.delete(query)

/**
 * Counts rows in the user table.
 * Promise will resolve with the number of user table rows matching the provided query.
 * @param {Query} query
 * @returns {Promise<{count: number}>}
 */
Users.count(query)
~~~

## Devices

~~~javascript
/**
 * Represents the device table.
 * @returns {Devices}
 */
ClearBladeAsync.Devices()

/**
 * @typedef {Object} DeviceInfo
 * @property {string} name
 * @property {string} active_key
 * @property {string} type
 * @property {string} state
 * @property {boolean} allow_key_auth
 * @property {boolean} allow_certificate_auth
 * @property {boolean} enabled
 */

/**
 * Creates a new device in the device table.
 * Promise will resolve with the device table row of the new device.
 * @param {DeviceInfo} info: The initial data describing the new device.
 * @returns {Promise<Object>}
 */
Devices.create(info)

/**
 * Fetches rows from the device table.
 * Promise will resolve with the device table rows matching the provided query.
 * @param {Query} query
 * @returns {Promise<Object[]>}
 */
Devices.read(query)

/**
 * Updates rows in the device table.
 * Promise will resolve empty.
 * @param {Query} query
 * @param {Object} changes
 * @returns {Promise<>}
 */
Devices.update(query, changes)

/**
 * Deletes rows from the device table.
 * Promise will resolve empty.
 * @param {Query} query
 * @returns {Promise<>}
 */
Devices.delete(query)

/**
 * Gets information about the devices connected to the platform via MQTT.
 * Promise will resolve with the connected device's information.
 * @returns {Promise<Object>}
 */
Devices.connectedDevices()

/**
 * Gets information about a single device connected to the platform via MQTT.
 * Promise will resolve with the connected device's information.
 * @param {string} deviceName
 * @returns {Promise<Object>}
 */
Devices.deviceConnections(deviceName)

/**
 * Gets the number of devices connected to the platform via MQTT.
 * Promise will resolve with the number of connected devices.
 * @returns {Promise<Object>}
 */
Devices.connectedDeviceCount()
~~~

## Auth

~~~javascript
/**
 * Authorization module.
 * @returns {Auth}
 */
ClearBladeAsync.Auth()

/**
 * @typedef {Object} AuthResponse
 * @property {string} auth_token
 * @property {string} refresh_token
 * @property {number} expiry: The time auth_token expires, given in Unix seconds
 */

/**
 * Adds a new anonymous user session.
 * @returns {Promise<AuthResponse>}
 */
Auth.authAnon()

/**
 * Adds a new device session.
 * @returns {Promise<AuthResponse>}
 */
Auth.authDevice(deviceName, deviceKey)

/**
 * Adds a new user session.
 * @returns {Promise<AuthResponse>}
 */
Auth.authUser(email, password)

/**
 * Adds a new user or device session using a refresh token instead of credentials.
 * @returns {Promise<AuthResponse>}
 */
Auth.reauth(refreshToken)

/**
 * Adds a new user session without requiring the user's credentials.
 * This function may only be called as a developer.
 * @returns {Promise<AuthResponse>}
 */
Auth.impersonateUser(userID)

/**
 * Finds the userID or deviceKey associated with the given auth token.
 * @returns {Promise<string>}
 */
Auth.userIDFromToken(token)
~~~

## Roles/Perms

~~~javascript
/**
 * Represents the roles table.
 * @returns {Roles}
 */
ClearBladeAsync.Roles()

/**
 * Creates a new role.
 * Promise resolves with the new role's ID.
 * @param {Object} info
 * @param {string} info.name: The new role's name
 * @param {string} [info.description]: The new role's optional description
 * @returns {Promise<string>}
 */
Roles.create(info)

/**
 * @typedef {Object} RoleRow
 * @prop {string} role_id
 * @prop {string} name
 * @prop {string} description
 */

/**
 * Queries the roles table.
 * Promise resolves with the requested role table rows.
 * @param {Query} query
 * @returns {Promise<RoleRow[]>}
 */
Roles.read(query)

/**
 * Updates roles in the roles table.
 * @param {Query} query
 * @param {Object} changes
 * @returns {Promise<>}
 */
Roles.update(query, changes)

/**
 * Deletes roles from the roles table.
 * Promise resolves empty.
 * @param {Query} query
 * @returns {Promise<>}
 */
Roles.delete(query)

/**
 * Finds roles granted to a given device or user.
 * Promise resolves with a list of role IDs.
 * @param {string} deviceNameOrUserID
 * @returns {Promise<string[]>}
 */
Roles.grantedTo(deviceNameOrUserID)

/**
 * Represents a specific role.
 * @param {string} roleID
 */
ClearBladeAsync.Role(roleID)

/**
 * Creates a new role, then copies all permissions from the parent role.
 * Promise resolves with the newly created role's ID.
 * @param {Object} info
 * @param {string} info.name: The new role's name
 * @param {string} [info.description]: The new role's optional description
 * @returns {Promise<string>}
 */
Role.duplicate(info)

/**
 * @typedef {Object} Permission
 * @prop {string} type: Resource type
 * @prop {string} [name]: Resource name
 * @prop {number} level: Permission level
 */

/**
 * Lists permissions in the role.
 * @returns {Promise<Permission[]>}
 */
Role.permissions()

/**
 * Overwrites the level of the given permissions.
 * Resources not included in the perms object remain unchanged.
 * Promise resolves empty.
 * @param {Permission|Permission[]} perms
 * @returns {Promise<>}
 */
Role.setPermissions(perms)

/**
 * Increases the level of the given permissions.
 * Resources not included in the perms object remain unchanged.
 * Promise resolves empty.
 * @param {Permission|Permission[]} perms
 * @returns {Promise<>}
 */
Role.increasePermissions(perms)

/**
 * Decreases the level of the given permissions.
 * Resources not included in the perms object remain unchanged.
 * Promise resolves empty.
 * @param {Permission|Permission[]} perms
 * @returns {Promise<>}
 */
Role.decreasePermissions(perms)

/**
 * Removes all permissions from the role.
 * Promise resolves empty.
 * @returns {Promise<>}
 */
Role.removeAllPermissions()

/**
 * Applies the role to the given device or user.
 * Promise resolves empty.
 * @param {string} deviceNameOrUserID
 * @returns {Promise<>}
 */
Role.applyTo(deviceNameOrUserID)

/**
 * Removes the role from the given device or user.
 * Promise resolves empty.
 * @param {string} deviceNameOrUserID
 * @returns {Promise<>}
 */
Role.stripFrom(deviceNameOrUserID)

/**
 * Finds all users with the given role.
 * Promise resolves with a list of user IDs.
 * @returns {Promise<string[]>}
 */
Role.users()

/**
 * Finds all devices with the given role.
 * Promise resolves with a list of device names.
 * @returns {Promise<string[]>}
 */
Role.devices()

/**
 * Enum helper for permission levels.
 * @readonly
 * @enum {number}
 */
ClearBladeAsync.Permissions = {
  READ:   1,
  CREATE: 2,
  UPDATE: 4,
  DELETE: 8,
}
~~~

## Caches

~~~javascript
/**
 * Represents a distributed cache.
 * @param {string} name
 * @returns {Cache}
 */
ClearBladeAsync.Cache(name)

/**
 * Sets a value in the cache.
 * Promise resolves empty.
 * @param {string} key
 * @param {*} value
 * @returns {Promise<>}
 */
Cache.set(key, value)

/**
 * Sets a value in the cache if it does not already exist.
 * Promise resolves with true if it sets the value, false if it already exists.
 * @param {string} key
 * @param {*} value
 * @returns {Promise<boolean>}
 */
Cache.setnx(key, value)

/**
 * Retrieves a value from the cache.
 * Promise resolves with the cached value.
 * @param {string} key
 * @returns {Promise<*>}
 */
Cache.get(key)

/**
 * Retrieves all key/value pairs from the cache.
 * Promise resolves with the object containing all cache values.
 * @returns {Promise<Object>}
 */
Cache.getAll()

/**
 * Removes a value from the cache.
 * Promise resolves empty.
 * @param {string} key
 * @returns {Promise<>}
 */
Cache.delete(key)

/**
 * Removes all values from the cache.
 * Promise resolves empty.
 * @returns {Promise<>}
 */
Cache.flush()

/**
 * Lists the keys which contain values in the cache.
 * Pattern may contain '?' as a single-character wildcard,
 * or '*' as a multi-character wildcard.
 * Promise resolves with a list of keys matching the given pattern.
 * @param {string} pattern
 * @returns {Promise<string[]>}
 */
Cache.keys(pattern)
~~~

## Triggers

~~~javascript
/**
 * Represents the triggers in your system.
 * @returns {Triggers}
 */
ClearBladeAsync.Triggers()

/**
 * @typedef {Object} TriggerInfo
 * @property {strign} name
 * @property {string} service_name: Which service to run when the trigger fires
 * @property {string} def_module: See the following table
 * @property {string} def_name: See the following table
 * @property {Object} key_value_pairs: See the following table
 */

/**
 * Creates a new trigger in your system.
 * Promise will resolve with the trigger data of the new trigger.
 * @param {TriggerInfo} info: Initial data describing the new trigger.
 * @returns {Promise<Object>}
 */
Triggers.create(info)

/**
 * Fetches triggers from your system.
 * Promise will resolve with the triggers matching the provided query.
 * @param {Query} query
 * @returns {Promise<Object[]>}
 */
Triggers.read(query)

/**
 * Updates triggers in your system.
 * Promise will resolve with the updated triggers.
 * @param {Query} query
 * @param {Object} changes
 * @returns {Promise<Object[]>}
 */
Triggers.update(query, changes)

/**
 * Deletes triggers from your system.
 * Promise will resolve empty.
 * @param {Query} query
 * @returns {Promise<>}
 */
Triggers.delete(query)
~~~

Valid trigger definitions:

|def_module|def_name|possible key_value_pairs keys|
|---|---|---|
|Asset|AssetCreated|assetClass|
|Asset|AssetDeleted|assetClass, assetID|
|Asset|AssetUpdated|assetClass, assetID|
|Data|CollectionCreated||
|Data|CollectionDeleted|collectionId, collectionName|
|Data|CollectionUpdated|collectionId, collectionName|
|Data|ItemCreated|collectionId, collectionName|
|Data|ItemDeleted|collectionId, collectionName, itemId|
|Data|ItemUpdated|collectionId, collectionName, itemId|
|Data|ItemUpserted|collectionId, collectionName, itemId|
|Device|DeviceCreated||
|Device|DeviceDeleted|deviceName|
|Device|DeviceUpdated|deviceName|
|File|FileCreated|filePath|
|File|FileDeleted|filePath|
|File|FileUpdated|filePath|
|Messaging|MQTTDeviceConnected|deviceName|
|Messaging|MQTTDeviceDisconnected|deviceName|
|Messaging|MQTTUserConnected|email|
|Messaging|MQTTUserDisconnected|email|
|Messaging|Publish|topic|
|Messaging|Subscribe|topic|
|Messaging|Unsubscribe|topic|
|StartConnectDisconnect|EdgeConnectedOnPlatform|edgeName|
|StartConnectDisconnect|EdgeDisconnectedOnPlatform|edgeName|
|StartConnectDisconnect|EdgeStarted||
|StartConnectDisconnect|PlatformConnectedOnEdge||
|StartConnectDisconnect|PlatformDisconnectedOnEdge||
|StartConnectDisconnect|PlatformStarted||
|User|UserCreated||
|User|UserDeleted|userId|
|User|UserUpdated|userId|

## Timers

~~~javascript
/**
 * Represents the timer table.
 * @returns {Timers}
 */
ClearBladeAsync.Timers()

/**
 * @typedef {Object} TimerInfo
 * @property {string} name
 * @property {string} description
 * @property {string} service_name: Which service to run when the timer fires
 * @property {string} start_time: RFC3339 "YYYY-MM-DD HH:MM:SS" or "now"
 * @property {number} repeats: How many times the timer should fire (-1 for infinite)
 * @property {number} frequency: How often the timer should fire in seconds
 * @property {string} [user_id]: Which user to run the service as, defaults to caller
 * @property {number} [user_type]: User type of user_id (1=dev, 2=user, 3=device)
 */

/**
 * Creates a new timer in the timer table.
 * Promise will resolve with the timer table row of the new timer.
 * @param {TimerInfo} info: Initial data describing the new timer.
 * @returns {Promise<Object>}
 */
Timers.create(info)

/**
 * Fetches rows from the timer table.
 * Promise will resolve with the timer table rows matching the provided query.
 * @param {Query} query
 * @returns {Promise<Object[]>}
 */
Timers.read(query)

/**
 * Updates rows in the timer table.
 * Promise will resolve with the updated timer table rows.
 * @param {Query} query
 * @param {Object} changes
 * @returns {Promise<Object[]>}
 */
Timers.update(query, changes)

/**
 * Deletes rows from the timer table.
 * Promise will resolve empty.
 * @param {Query} query
 * @returns {Promise<>}
 */
Timers.delete(query)
~~~

## Edges

~~~javascript
/**
 * Represents the edge table.
 * @returns {Edges}
 */
ClearBladeAsync.Edges()

/**
 * @typedef {Object} EdgeInfo
 * @property {string} name
 * @property {string} token
 */

/**
 * Creates a new edge in the edge table.
 * Promise will resolve with the edge table row of the new edge.
 * @param {EdgeInfo} info: Initial data describing the new edge.
 * @returns {Promise<Object>}
 */
Edges.create(info)

/**
 * Fetches rows from the edge table.
 * Promise will resolve with the edge table rows matching the provided query.
 * @param {Query} query
 * @returns {Promise<Object[]>}
 */
Edges.read(query)

/**
 * Updates rows in the edge table.
 * Promise will resolve with the updated edge table rows.
 * @param {Query} query
 * @param {Object} changes
 * @returns {Promise<Object[]>}
 */
Edges.update(query, changes)

/**
 * Deletes rows from the edge table.
 * Promise will resolve empty.
 * @param {Query} query
 * @returns {Promise<>}
 */
Edges.delete(query)
~~~

## Adapters

[Examples](#adapter-examples)

~~~javascript
/**
 * Represents the adapters table
 * @returns {Adapters}
 */
ClearBladeAsync.Adapters()

/**
 * @typedef {object} AdapterMeta
 * @property {string} name
 * @property {string} [description]
 * @property {string} [protocol]
 * @property {string} architecture
 * @property {string} os
 * @property {string} deploy_command
 * @property {string} start_command
 * @property {string} stop_command
 * @property {string} status_command
 * @property {string} deploy_command
 * @property {string} undeploy_command
 * @property {string} logs_command
 * @property {boolean} [run_stop_on_deploy]
 * @property {boolean} [run_start_on_deploy]
 * @property {boolean} [run_deploy_on_deploy]
 */

/**
 * Creates a new adapter.
 * Promise will return with the newly created adapter's meta.
 * @param {AdapterMeta} options
 * @returns {Promise<AdapterMeta>}
 */
Adapters.create(options)

/**
 * Reads all meta from the adapter table.
 * Promise will return with a list of adapter metas.
 * @returns {Promise<AdapterMeta[]>}
 */
Adapters.list()

/**
 * Reads a single adapter's meta.
 * Promise will resolve with the queried adapter's meta.
 * @param {string} name
 * @returns {Promise<AdapterMeta>}
 */
Adapters.read(name)

/**
 * Updates an adapter's meta.
 * Promise will resolve with the updated adapter meta.
 * @param {string} name
 * @param {object} changes
 * @returns {Promise<AdapterMeta>}
 */
Adapters.update(name, changes)

/**
 * Deletes an adapter.
 * Promise will resolve empty.
 * @param {string} name
 * @returns {Promise<>}
 */
Adapters.delete(name)

/**
 * Represents a single adapter.
 * @returns <Adapter>
 */
ClearBladeAsync.Adapter(name)

/**
 * Reads the given adapter's meta.
 * Promise will resolve with the adapter's meta.
 * @returns {Promise<AdapterMeta>}
 */
Adapter.read()

/**
 * Updates the given adapter's meta.
 * Promise will resolve with the updated adapter meta.
 * @param {object} changes
 * @returns {Promise<AdapterMeta>}
 */
Adapter.update(changes)

/**
 * Deletes the given adapter.
 * Promise will resolve empty.
 * @returns {Promise<>}
 */
Adapter.delete()

/**
 * @typedef {object} AdapterFileMeta
 * @property {string} name
 * @property {string} path_name
 * @property {string} owner
 * @property {string} group
 * @property {string} permissions
 * @property {string|Uint8Array} file
 */

/**
 * Creates a file attached to the given adapter.
 * Promise will resolve empty.
 * @param {AdapterFileMeta} options
 * @returns {Promise<>}
 */
Adapter.createFile(options)

/**
 * Reads a file attached to the given adapter.
 * Promise will resolve with the queried file meta.
 * If encoding is provided, file contents are decoded into a string.
 * Otherwise, file contents will be a Uint8Array.
 * @param {string} filename
 * @param {string} [encoding]
 * @returns {Promise<AdapterFileMeta>}
 */
Adapter.readFile(filename[, encoding])

/**
 * Lists all files attached to the given adapter.
 * Promise will resolve with a list of file meta.
 * File contents are not returned.
 * @returns {Promise<AdapterFileMeta[]>}
 */
Adapter.listFiles()

/**
 * Updates a file attached to the given adapter.
 * Promise will resolve with the updated file meta.
 * If encoding is provided, the returned file contents will be decoded into a string.
 * Otherwise, the returned file contents will be a Uint8Array.
 * @param {string} filename
 * @param {object} changes
 * @param {string} [encoding]
 * @returns {Promise<AdapterFileMeta>}
 */
Adapter.updateFile(filename, changes[, encoding])

/**
 * Deletes a file attached to the given adapter.
 * Promise will resolve empty.
 * @param {string} filename
 * @returns {Promise<>}
 */
Adapter.deleteFile(filename)

/**
 * @typedef {object} AdapterControlResponse
 * @property {string} err: Will contain an error if the control command fails
 * @property {string} command: The shell command which got executed
 * @property {string} output: The combined stdout and stderr output resulting from executing the shell command
 */

/**
 * Sends a control command to the given edges.
 * Valid commands are: "start", "stop", "restart", "status", "undeploy", "logs".
 * The edges param is an array of edge names.
 * If run on the edge, the edges param may only include the current edge's name.
 * Promise will resolve with an object whose keys are edge names and whose values are AdapterControlResponses.
 * @param {string} command
 * @param {string[]} edges
 * @returns {Promise<object>}
 */
Adapter.control(command, edges)
~~~

## Collection custom sync

~~~javascript
/**
 * Initializes the custom sync object
 * @property {string}  CustomSync.Platform: Destination type
 * @property {string}  CustomSync.AllEdges: Destination type
 * @property {string}  CustomSync.Now: Interval type
 * @returns {CustomSync}
 */
ClearBladeAsync.CustomSync()

/**
 * Sends data to the destination at the specified interval
 * @param {string} collectionName: Collection name of the data you are trying to sync
 * @param {Object} data: The actual item or items created/updated/deleted
 * @param {string} destination: Can be CustomSync.Platform if on the edge or CustomSync.AllEdges or an individual edge name if on the platform
 * @param {string} [interval=now]: Optional but can be CustomSync.Now or be actual interval string values like “60s”, “10m”, “1h”, “5d”. Only seconds, minutes, hours, and days are supported. If the interval is not specified, CustomSync.Now is the default.
 * @returns {Promise<>}
 */
CustomSync.sync(collectionName, data, destination, interval)
~~~
The data sent using the `sync` function can be captured on the receiving end by subscribing to the custom sync topic `$custom_sync/receive`. 
> Note 1: Your code service name needs to have the prefix `ClearBladeCustomSync_` to be able to subscribe to the custom sync topic.  
> Note 2: Triggers for collection item create/update/delete are disabled for a service name with the `ClearBladeCustomSync_` prefix to avoid an infinite sync loop. 

## Secret

~~~javascript
/**
 * Initializes the Secret object
 * @returns {Secret}
 */
ClearBladeAsync.Secret()

/**
 * Encrypts the secret and stores it in the database
 * @param {string} name: The secret's name
 * @param {*} data: Secret data
 * @returns {Promise<>}
 */
Secret.create(name, data)

/**
 * Retrieves encrypted secret from the database, decrypts it, and returns plaintext
 * @param {string} name: The secret's name
 * @returns {Promise<*>}
 */
Secret.read(name)

/**
 * Retrieves encrypted secret(s) from the database, decrypts and returns name/plaintext object
 * @param {Query} query object
 * @returns {Promise<Object>}
 * Returned object has "name" -> secret name and "data" -> secret data
 */
Secret.readWithQuery(query)

/**
 * Encrypts secret and updates existing secret in the database
 * @param {string} name: The secret's name
 * @param {*} data: Secret data
 * @returns {Promise<>}
 */
Secret.update(name, data)

/**
 * Deletes secret from the database
 * @param {string} name: The secret's name
 * @returns {Promise<>}
 */
Secret.delete(name)

/**
 * Deletes secret(s) from the database
 * @param {Query} query object
 * @returns {Promise<>}
 */
Secret.deleteWithQuery(query)
~~~

## Preloader
The Preloader object enables a stream service to listen and respond to HTTP requests like a regular microservice. The advantage of this is the reduced overhead of spawning and tearing down a microservice when called repeatedly. 

~~~javascript
/**
 * Initializes the preloader object
 * @returns {Preloader}
 */
ClearBladeAsync.Preloader()

/**
 * Preloader starts listening for HTTP requests
 * @param {function} onRequest: Function to handle HTTP request
 * @returns {Promise<>}
 */
Preloader.listen(onRequest)
~~~

## Google Cloud Logger

~~~javascript
/**
 * Initializes the GoogleCloudLogger object
 * @returns {GoogleCloudLogger}
 */
 ClearBladeAsync.GoogleCloudLogger()

/**
 * @typedef {object} AdminAuditLogInfo
 * @property {string} service_name: The name of the API service performing the operation
 * @property {string} method_name: The name of the operation
 * @property {string} requester_email: The email of the dev or user who requested the operation
 * @property {string} permission_name: The permission name that this operation requires
 * @property {string} device_name: The device's name
 * @property {string} device_num_id: The device's num_id
 * @property {object} request: Any request data you want to log
 * @property {object} [response]: Any response data you want to log
 */

/**
 * Sends an admin audit log to Google Cloud Logging if it's set up.
 * The caller is responsible for sanitizing the data sent to the logger, which will be forwarded verbatim. 
 * @param{AdminAuditLogInfo} info
 * @param{string} [project] if multiple cloud loggers are set up for a system, this param will choose where to send the log.
 * @returns {Promise<>}
 */
GoogleCloudLogger.adminAuditLog(info[, project])

/**
 * @typedef {object} DeviceEventLogInfo
 * @property {string} device_name: The device's name performing the event
 * @property {string} event_type: The event's name
 * @property {boolean} success: If the event succeeded or not
 * @property {object} payload: Any event data you want to log
 */

/**
 * Sends a device event log to Google Cloud Logging if it's set up.
 * The caller is responsible for sanitizing the data sent to the logger, which will be forwarded verbatim. 
 * The payload's resourceName field will be auto-populated if not provided.
 * @param{DeviceEventLogInfo} info
 * @param{string} [project] if multiple cloud loggers are set up for a system, this param will choose where to send the log.
 * @returns {Promise<>}
 */
GoogleCloudLogger.deviceEventLog(info[, project])
~~~

## Data usage

~~~javascript
/**
 * Initializes the DataUsage object
 * @returns {DataUsage}
 */
 ClearBladeAsync.DataUsage()

/**
 * Reports the HTTP data size to the platform
 * @param {string} systemKey
 * @param {integer} size
 * @returns {Promise<>}
 */
DataUsage.reportHTTPDataUsage(systemKey, size)
~~~

## Google Cloud Monitoring

~~~javascript
/**
 * Initializes the GoogleCloudMonitoring object
 * @property {string}  GoogleCloudMonitoring.Protocol.MQTT: Protocol type
 * @property {string}  GoogleCloudMonitoring.Protocol.HTTP: Protocol type
 * @property {string}  GoogleCloudMonitoring.Direction.INGRESS: Network data direction
 * @property {string}  GoogleCloudMonitoring.Direction.EGRESS: Network data direction
 * @property {string}  GoogleCloudMonitoring.OperationType.EVENT: Operation type event
 * @property {string}  GoogleCloudMonitoring.OperationType.STATE: Operation type state
 * @property {string}  GoogleCloudMonitoring.OperationType.HEARTBEAT: Operation type heartbeat
 * @property {string}  GoogleCloudMonitoring.OperationType.SUBSCRIBE: Operation type subscribe
 * @property {string}  GoogleCloudMonitoring.OperationType.UNSUBSCRIBE: Operation type unsubscribe
 * @property {string}  GoogleCloudMonitoring.OperationType.CONFIG_SENT: Operation type config_sent
 * @property {string}  GoogleCloudMonitoring.OperationType.CONFIG_ACK: Operation type config_ack
 * @property {string}  GoogleCloudMonitoring.OperationType.COMMAND_SENT: Operation type command_sent
 * @property {string}  GoogleCloudMonitoring.OperationType.COMMAND_ACK: Operation type command_ack
 * @property {string}  GoogleCloudMonitoring.OperationType.CONNECTION_OPEN: Operation type connection_open
 * @property {string}  GoogleCloudMonitoring.OperationType.DISCONNECT: Operation type disconnect
 * @property {string}  GoogleCloudMonitoring.ErrorType.TELEMETRY_PUBLISH_INTERNAL: Error type telemetry_publish_internal
 * @property {string}  GoogleCloudMonitoring.ErrorType.TELEMETRY_PUBLISH_USER: Error type telemetry_publish_user
 * @property {string}  GoogleCloudMonitoring.ErrorType.SET_STATE_INTERNAL: Error type set_state_internal
 * @property {string}  GoogleCloudMonitoring.ErrorType.SET_STATE_USER: Error type set_state_user
 * @property {string}  GoogleCloudMonitoring.ErrorType.INCOMING_BUFFER_FULL: Error type incoming_buffer_full
 * @returns {GoogleCloudMonitoring}
 */
 ClearBladeAsync.GoogleCloudMonitoring()

/**
 * Reports the active devices metric.
 * @param{Protocol} protocol
 * @returns {Promise<>}
 */
GoogleCloudMonitoring.reportActiveDevicesMetric(protocol)

/**
 * Reports the billing bytes count metric.
 * @param{Protocol} protocol
 * @param{Direction} direction
 * @param{integer} bytescount
 * @returns {Promise<>}
 */
GoogleCloudMonitoring.reportBillingBytesCountMetric(protocol, direction, bytescount)

/**
 * Reports the error count metric.
 * @param{Protocol} protocol
 * @param{ErrorType} errorType
 * @returns {Promise<>}
 */
GoogleCloudMonitoring.reportErrorCountMetric(protocol, errorType)

/**
 * Reports the operation count metric.
 * @param{OperationType} operationType
 * @returns {Promise<>}
 */
GoogleCloudMonitoring.reportOperationCountMetric(operationType)

/**
 * Reports the received bytes count metric.
 * @param{integer} bytescount
 * @returns {Promise<>}
 */
GoogleCloudMonitoring.reportReceivedBytesCountMetric(bytesCount)

/**
 * Reports the sent bytes count metric.
 * @param{integer} bytescount
 * @returns {Promise<>}
 */
GoogleCloudMonitoring.reportSentBytesCountMetric(bytesCount)
~~~

# Examples

## Collection examples

~~~javascript
var assetsCollection = ClearBladeAsync.Collection('assets');

/**
 * @typedef {Object} AssetRow: A row in the assets collection
 * @property {string} asset_id: Each asset's unique identifier
 * @property {string} asset_type: Can be "thermometer" or "barometer"
 * @property {number} reading: The temperature or pressure value
 * @property {string} last_updated: Timestamp when the asset last sent an update
 * @property {boolean} stale: Indicates the asset hasn't sent an update in over a week
 */

/**
 * updateStaleAssets uses a query to find all rows in the assets collection
 * whose last_updated value is more than a week old, then updates those rows
 * setting their stale value to true.
 * @return {Promise<AssetRow[]>} all stale assets
 */
function updateStaleAssets() {
    var lastWeek = new Date(new Date().getTime() - 7 * 24 * 60 * 60 * 1000);
    var staleQuery = ClearBladeAsync.Query().lessThan('last_updated', lastWeek.toISOString())
    return assetsCollection.update(staleQuery, {stale: true});
}

/**
 * updateStaleAssetsV2 is the same as updateStaleAssets, but it uses
 * a query with multiple conditions to only update assets whose stale value
 * is not already true.
 * @return {Promise<AssetRow[]>} the assets which became stale in the past week
 */
function updateStaleAssetsV2() {
    var lastWeek = new Date(new Date().getTime() - 7 * 24 * 60 * 60 * 1000);
    var staleQuery = ClearBladeAsync.Query();
    staleQuery.lessThan('last_updated', lastWeek.toISOString());
    staleQuery.equalTo('stale', false);
    return assetsCollection.update(staleQuery, {stale: true});
}

/**
 * reportDangerousTemperatures uses an or query to find all thermometers
 * whose reading is below freezing or above boiling.
 * @return {Promise<AssetRow[]>} thermometer assets with dangerous temperatures
 */
function reportDangerousTemperatures() {
    var lowTempQuery = ClearBladeAsync.Query();
    lowTempQuery.equalTo('asset_type', 'thermometer');
    lowTempQuery.lessThanEqualTo('reading', 32.0);

    var highTempQuery = ClearBladeAsync.Query();
    highTempQuery.equalTo('asset_type', 'thermometer');
    highTempQuery.greaterThanEqualTo('reading', 212.0);

    var dangerQuery = lowTempQuery.or(highTempQuery);
    return assetsCollection.fetch(dangerQuery).then(function(results){
        log('We found', results.TOTAL, 'assets with dangerous temperatures!');
        return results.DATA;
    });
}

/**
 * processAssetMessage decodes a message sent by an asset,
 * and inserts it into the assets collection.
 * If the asset already exists in the collection, we update it.
 * @param  {Object} message: An MQTT message from an asset
 * @param  {string} message.id: The asset's unique identifier
 * @param  {string} message.type: The asset's type
 * @param  {number} message.reading: The asset's sensor reading
 * @return {Promise<AssetRow[]>} the new or updated asset row
 */
function processAssetMessage(message) {
    var asset = {
        asset_id: message.id,
        asset_type: message.type,
        reading: message.reading,
        last_updated: new Date().toISOString(),
        stale: false,
    }
    return assetsCollection.upsert(asset, 'asset_id');
}
~~~

## Database examples

~~~javascript
var db = ClearBladeAsync.Database();
var mongo = ClearBladeAsync.Database({externalDBName: 'ClearBladeMongo'});
var bigQuery = ClearBladeAsync.Database({externalDBName: 'ClearBladeBigQuery'});

/**
 * @typedef {Object} AssetCounts
 * @property {number} thermometer: The number of thermometers in the assets collection
 * @property {number} barometer: The number of barometers in the assets collection
 */

/**
 * countAssetsByType runs a raw SQL query against the assets collection
 * to count the number of each asset_type.
 * @return {AssetCounts} counts per asset type
 */
function countAssetsByType() {
    var sqlQuery = 'SELECT json_object_agg(asset_type, count) counts FROM (SELECT asset_type, COUNT(asset_type) FROM assets GROUP BY asset_type) foo;';
    return db.query(sqlQuery).then(function(results){
        if(results.length !== 1) {
            throw new Error('countAssetsByType: unexpected number of rows returned: '+JSON.stringify(results));
        }
        return JSON.parse(results[0].counts);
    })
}

/**
 * findNewDevices runs a raw SQL query against the device table
 * to find all devices created in the past seven days whose type is in the allowedDeviceTypes list.
 * @param {[]string} allowedDeviceTypes
 * @returns {Promise<[]{name: string, type: string, state: string, created_date: number}>}
 */
 function findNewDevices(allowedDeviceTypes) {
     var sqlQuery = 'SELECT name, type, state, created_date FROM devices WHERE created_date > $1 AND type = ANY($2)';
     var oneWeekAgo = new Date();
     oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
     var oneWeekAgoUnixMS = Math.floor(oneWeekAgo.getTime()/1000);
     return db.query(q, oneWeekAgoUnixMS, allowedDeviceTypes);
}

/**
 * insertAssetHistory performs a raw MongoDB operation to insert a message into the assetHistory collection.
 * @param  {Object} message: An MQTT message from an asset
 * @return {Promise<>}
 */
function insertAssetHistory(message) {
    var mongoInsertQuery = 'db.assetHistory.insert('+JSON.stringify(message)+')';
    return mongo.performOperation(mongoInsertQuery);
}

/**
 * addHistoryToBigQuery inserts an MQTT message into a BigQuery table.
 * @param  {Object} message: An asset's MQTT message
 * @return {Promise<>}
 */
function addHistoryToBigQuery(message) {
    var insertObject = {
        dataset: "history",
        table: "mqtt_messages",
        data: message,
    }
    return bigQuery.performOperation("insert", insertObject);
}

/**
 * updateUserEmail updates a user's email and records the action in the audit table.
 * @param {uuid} user_id
 * @param {string} new_email
 * @return {Promise<>}
 */
function updateUserEmail(user_id, new_email) {
    return db.transaction([
        db.statement("UPDATE users SET email = $1 WHERE user_id = $2", new_email, user_id),
        db.statement("INSERT INTO audit(user_id, action, time) VALUES ($1, $2, $3)", user_id, "email updated", new Date()),
    ]);
}
~~~

## File management examples

~~~javascript
/**
 * sendAdapterLogsToPlatform copies a log file from an edge's filesystem into the edge's outbox.
 * This file will be synced to the platform.
 * @return {Promise<>}
 */
function sendAdapterLogsToPlatform() {
    var fs = ClearBladeAsync.FS('myDeployment');
    return fs.copyFile('/home/clearblade/myAdapter.log', 'outbox/myAdapter.log');
}

/**
 * checkAdapterLogsForErrors reads a log file from the platform's inbox,
 * and returns the lines from those logs containing the "ERROR" string.
 * @return {Promise<string[]>} the error logs
 */
function checkAdapterLogsForErrors() {
    var file = ClearBladeAsync.File('myDeployment', 'inbox/myEdge/myAdapter.log');
    return file.read('utf8')
        .then(function(logString){
            var logLines = logString.split('\n');
            var errorLines = logLines.filter(function(line){return line.indexOf('ERROR') !== -1});
            return errorLines;
        })
}
~~~

## Adapter examples

~~~javascript
var systemWatcherAdapter = ClearBladeAsync.Adapter('systemWatcher');

/**
 * updateAdapterStatusFile updates the status command of the systemWatcherAdapter
 * to print the statuses of the given servicesToWatch.
 * @param {string[]} the services to watch
 * @returns {Promise<AdapterFileMeta>} the updated adapter metadata
 */
function updateAdapterStatusFile(servicesToWatch) {
  if(servicesToWatch.length===0) return Promise.resolve();
  var file = '#!/usr/bin/env bash\n\n';
  for (var i = 0; i < services.length; i++) {
      file += 'systemctl --no-pager -l status ' + services[i] + '\n';
  }
  return systemWatcherAdapter.updateFile('status.sh', {file: file});
}

/**
 * statusFetcher runs the status command of the systemWatcher adapter,
 * and reports back the result.
 * @param {string} currentEdgeName
 * @returns {Promise<string>} status.sh stdout
 */
function periodicStatusFetcher(currentEdgeName) {
  return systemWatcherAdapter.control('status', [currentEdgeName])
    .then(function(results){
      var myResults = results[currentEdgeName];
      if(myResults.err !== "") throw new Error("status check failed: "+myResults.err);
      return myResults.output;
    })
}
~~~

## Preloader examples

~~~javascript
function myStreamService(req, resp) {
  var preloader = ClearBladeAsync.Preloader();
  var onRequest = function(newReq, newResp) {
    // dosomething
    newResp.success("Yay!");
  };
  preloader.listen(onRequest).catch(function(e) {
    resp.error(e);
  });
}
~~~

~~~javascript
function myStreamService(req, resp) {
  var preloader = ClearBladeAsync.Preloader();
  var onRequest = function(newReq, newResp) {
    // dosomething
    newResp.status(200);
    newResp.send("Yay!");
    return;
  };
  preloader.listen(onRequest).catch(function(e) {
    resp.error(e);
  });
}
~~~

The onRequest function receives a new request and response object. The methods on the new response object don't exit the service; they send the response back via HTTP. Whereas the original response object, if called, will exit the service.
