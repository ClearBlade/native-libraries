The ClearBlade Async library works with the event loop to provide asynchronous ClearBlade functions.
The `init` function is not required.

__Reference__
1. [Query](#query)
1. [Collections](#collections)
1. [Databases](#databases)
1. [File Management](#file-management)
1. [Locks](#locks)
1. [Users](#users)
1. [Devices](#devices)
1. [Auth](#auth)
1. [Roles/Perms](#rolesperms)
1. [Caches](#caches)
1. [Triggers](#triggers)
1. [Timers](#timers)
1. [Edges](#edges)
1. [Adapters](#adapters)

__Examples__
1. [Collections](#collection-examples)
1. [Databases](#database-examples)
1. [File Management](#file-management-examples)
1. [Adapter Examples](#adapter-examples)

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
 * @returns {Query} with filter applied
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
 * @param {number} pageSize - the max number of rows to return
 * @param {number} pageNum - number of pages to offset return data (1 = first page)
 * @returns {Query} with limits applied
 */
Query.setPage(pageSize, pageNum)

/**
 * Specifies how the returned data should be sorted.
 * @param {string} column - the name of the column which should used to sort
 * @returns {Query} with sorting applied
 */
Query.ascending(column)
Query.descending(column)

/**
 * Limits the data returned to only include the given columns.
 * @param {string[]} columns - list of column names
 * @returns {Query} with filter applied
 */
Query.columns(columns)

/**
 * Sets a query string to be used in certain mongodb operations.
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
 * Promise resolves with object containing the new collection's metadata.
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
 * @property {Object[]} DATA - requested collection rows
 * @property {number} TOTAL - number of objects in the DATA field
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
 * Existence is determined by the conflictColumn, which must either be a primary key or have a unique index on it.
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
 * Promise resolves with list of column metadata.
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
 * and enforces each entry in the indexed column is unique.
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
 * Represents a Database.
 * If options specifies externalDBName, database represents external database.
 * If options empty, database represents the ClearBlade system's database.
 * @param {{externalDBName: string}} options
 * @returns {Database}
 */
ClearBladeAsync.Database(options)

/**
 * Performs a sql query on ClearBlade collections.
 * Promise resolves with the requested database rows.
 * @param {string} rawQuery
 * @param {...*} [params] optional parameters for query
 * @returns {Promise<Object[]>}
 */
Database.query(rawQuery, params)

/**
 * Executes a sql operation on ClearBlade collections.
 * Promise resolves with the count of rows effected.
 * No database rows are returned with this function.
 * @param {string} rawQuery
 * @param {...*} [params] optional parameters for query
 * @returns {Promise<{count: number}>}
 */
Database.exec(rawQuery, params)

/**
 * Performs a raw operation on an external database.
 * Promise resolves with the database operation's result.
 * @param {string} operation
 * @param {...*} [args] optional arguments to operation
 * @returns {Promise<*>}
 */
Database.performOperation(operation, args)
~~~

## File Management

Note: all file paths are relative to bucket root, except on an edge where you can use full paths.

[Examples](#file-management-examples)

~~~javascript
/**
 * Represents a sync'd filesystem.
 * @param {string} deployment_name
 * @returns {FS}
 */
ClearBladeAsync.FS(deployment_name)

/**
 * Reads the contents of a directory.
 * The promise is resolved with an array of file names in that directory.
 * @param {string} path
 * @returns {Promise<string[]>}
 */
FS.readDir(path)

/**
 * Reads the entire contents of a file.
 * The promise is resolved with the file contents.
 * If encoding is not specified, contents will be a UInt8Array.
 * If encoding is specified, contents will be a string.
 * @param {string} path
 * @param {string} [encoding]
 * @returns {Promise<string|UInt8Array>}
 */
FS.readFile(path[, encoding])

/**
 * Writes the given data to a file, replacing the file if it already exists.
 * The promise is resolved empty.
 * @param {string} path
 * @param {string|Uint8Array} data
 * @returns {Promise<>}
 */
FS.writeFile(path, data)

/**
 * Renames (mv's) oldPath to newPath.
 * The promise is resolved empty.
 * @param {string} oldPath
 * @param {string} newPath
 * @returns {Promise<>}
 */
FS.renameFile(oldPath, newPath)

/**
 * Copies srcPath to dstPath, overwriting dstPath if it already exists.
 * The promise is resolved empty.
 * @param {string} srcPath
 * @param {string} dstPath
 * @returns {Promise<>}
 */
FS.copyFile(srcPath, dstPath)

/**
 * Deletes the file at the specified path.
 * The promise is resolved empty.
 * @param {string} path
 * @returns {Promise<>}
 */
FS.deleteFile(path)

/**
 * @typedef {Object} FileStats
 * Represents file metadata.
 * @property {number} size - file size in bytes
 * @property {string} permissions - ex: "-rwxrwxrwx"
 */

/**
 * Retrieves metadata for the given file path.
 * @param {string} path
 * @returns {Promise<FileStats>}
 */
FS.stat(path)

/**
 * Represents a file on the filesystem.
 * Useful if you're doing multiple operations on a single file.
 * @param {string} deployment_name
 * @param {string} path
 * @returns {File}
 */
ClearBladeAsync.File(deployment_name, path)

/**
 * Retrieves metadata for the file.
 * @returns {Promise<FileStats>}
 */
File.stat()

/**
 * Reads the entire contents of the file.
 * The promise is resolved with the file contents.
 * If encoding is not specified, contents will be a UInt8Array.
 * If encoding is specified, contents will be a string.
 * @param {string} [encoding]
 * @returns {Promise<string|UInt8Array>}
 */
File.read([encoding])

/**
 * Writes the given data to the file, replacing the contents if it already exists.
 * The promise is resolved empty.
 * @param {string|Uint8Array} data
 * @returns {Promise<>}
 */
File.write(data)

/**
 * Renames (mv's) file to newPath.
 * The promise is resolved empty.
 * @param {string} newPath
 * @returns {Promise<>}
 */
File.rename(newPath)

/**
 * Copies file to dstPath, overwriting dstPath if it already exists.
 * The promise is resolved empty.
 * @param {string} dstPath
 * @returns {Promise<>}
 */
File.copy(dstPath)

/**
 * Deletes the file.
 * The promise is resolved empty.
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
 * @param {UserInfo} info - initial data describing the new user.
 * @returns {Promise<Object>}
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
 * @param {DeviceInfo} info - initial data describing the new device.
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
 * @property {number} expiry - time auth_token expires, given in unix seconds
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
 * Adds a new user session, without requiring the user's credentials.
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
 * @param {string} info.name - new role's name
 * @param {string} [info.description] - new role's optional description
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
 * Finds roles currently granted to given device or user.
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
 * @param {string} info.name - new role's name
 * @param {string} [info.description] - new role's optional description
 * @returns {Promise<string>}
 */
Role.duplicate(info)

/**
 * @typedef {Object} Permission
 * @prop {string} type - resource type
 * @prop {string} [name] - resource name
 * @prop {number} level - permission level
 */

/**
 * Lists permissions in role.
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
 * Sets a value in the cache, if it does not already exist.
 * Promise resolves with true if it set the value, false if it already existed.
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
 * Promise resolves with object containing all cache values.
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
 * Lists the keys which currently contain values in the cache.
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
 * @property {string} service_name - which service to run when trigger fires
 * @property {string} def_module - see following table
 * @property {string} def_name - see following table
 * @property {Object} key_value_pairs - see following table
 */

/**
 * Creates a new trigger in your system.
 * Promise will resolve with the trigger data of the new trigger.
 * @param {TriggerInfo} info - initial data describing the new trigger.
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
 * @property {string} service_name - which service to run when timer fires
 * @property {string} start_time - RFC3339 "YYYY-MM-DD HH:MM:SS" or "now"
 * @property {number} repeats - how many times the timer should fire (-1 for infinite)
 * @property {number} frequency - how often the timer should fire, in seconds
 * @property {boolean} [user_id] - which user to run the service as, defaults to caller
 * @property {number} [user_type] - user type of user_id (1=dev, 2=user, 3=device)
 */

/**
 * Creates a new timer in the timer table.
 * Promise will resolve with the timer table row of the new timer.
 * @param {TimerInfo} info - initial data describing the new timer.
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
 * @param {EdgeInfo} info - initial data describing the new edge.
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
 * @property {string} err - will contain an error if the control command fails
 * @property {string} command - the shell command which got executed
 * @property {string} output - the combined stdout and stderr output resulting from executing the shell command
 */

/**
 * Sends a control command to the given edges.
 * Valid commands are: "start", "stop", "restart", "status", "undeploy", "logs".
 * The edges param is an array of edge names.
 * If run on the edge, the edges param may only include the current edge's name.
 * Promise will resolve with an object whose keys are edge names and values are AdapterControlResponses.
 * @param {string} command
 * @param {string[]} edges
 * @returns {Promise<object>}
 */
Adapter.control(command, edges)
~~~

# Examples

## Collection Examples

~~~javascript
var assetsCollection = ClearBladeAsync.Collection('assets');

/**
 * @typedef {Object} AssetRow - a row in the assets collection
 * @property {string} asset_id - a unique identifier for each asset
 * @property {string} asset_type - can be "thermometer" or "barometer"
 * @property {number} reading - the current temperature or pressure value
 * @property {string} last_updated - timestamp when the asset last sent an update
 * @property {boolean} stale - indicates the asset hasn't sent an update in over a week
 */

/**
 * updateStaleAssets uses a query to find all rows in the assets collection
 * whose "last_updated" value is more than a week old, then updates those rows
 * setting their "stale" value to true.
 * @return {Promise<AssetRow[]>} all stale assets
 */
function updateStaleAssets() {
    var lastWeek = new Date(new Date().getTime() - 7 * 24 * 60 * 60 * 1000);
    var staleQuery = ClearBladeAsync.Query().lessThan('last_updated', lastWeek.toISOString())
    return assetsCollection.update(staleQuery, {stale: true});
}

/**
 * updateStaleAssetsV2 is the same as updateStaleAssets, but it uses
 * a query with multiple conditions to only update assets whose "stale" value
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
 * reportDangerousTemperatures uses an "or" query to find all thermometers
 * whose current reading is below freezing or above boiling.
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
 * @param  {Object} message - an MQTT message from an asset
 * @param  {string} message.id - the asset's unique identifier
 * @param  {string} message.type - the asset's type
 * @param  {number} message.reading - the asset's current sensor reading
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

## Database Examples

~~~javascript
var db = ClearBladeAsync.Database();
var mongo = ClearBladeAsync.Database({externalDBName: 'ClearBladeMongo'});
var bigQuery = ClearBladeAsync.Database({externalDBName: 'ClearBladeBigQuery'});

/**
 * @typedef {Object} AssetCounts
 * @property {number} thermometer - number of thermometers in the assets collection
 * @property {number} barometer - number of barometers in the assets collection
 */

/**
 * countAssetsByType runs a raw sql query against the assets collection
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
 * findNewDevices runs a raw sql query against the device table
 * to find all devices created in the past 7 days whose type is in the allowedDeviceTypes list.
 * @param {[]string} allowedDeviceTypes
 * @returns {Promise<[]{name: string, type: string, state: string, created_date: number}>}
 */
 function findNewDevices(allowedDeviceTypes) {
     var sqlQuery = 'SELECT name, type, state, created_date FROM devices WHERE created_date > $1 AND type = ANY($2)';
     var oneWeekAgo = new Date();
     oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
     var oneWeekAgoUnixMS = Math.floor(oneWeekAgo.getTime()/1000);
     return ClearBladeAsync.Database().query(q, oneWeekAgoUnixMS, allowedDeviceTypes);
}

/**
 * insertAssetHistory performs a raw MongoDB operation to insert a message into the assetHistory collection.
 * @param  {Object} message - an MQTT message from an asset
 * @return {Promise<>}
 */
function insertAssetHistory(message) {
    var mongoInsertQuery = 'db.assetHistory.insert('+JSON.stringify(message)+')';
    return mongo.performOperation(mongoInsertQuery);
}

/**
 * addHistoryToBigQuery inserts an MQTT message into a BigQuery table.
 * @param  {Object} message - an MQTT message from an asset
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
~~~

## File Management Examples

~~~javascript
/**
 * sendAdapterLogsToPlatform copies a log file from an edge's filesystem into the edge's outbox.
 * This file will be sync'd to the platform.
 * @return {Promise<>}
 */
function sendAdapterLogsToPlatform() {
    var fs = ClearBladeAsync.FS('myDeployment');
    return fs.copyFile('/home/clearblade/myAdapter.log', 'outbox/myAdapter.log');
}

/**
 * checkAdapterLogsForErrors reads a log file from the platform's inbox,
 * and returns the lines from those logs that contain the string "ERROR".
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

## Adapter Examples

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
