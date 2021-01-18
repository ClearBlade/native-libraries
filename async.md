The ClearBlade Async library works with the event loop to provide asynchronous ClearBlade functions. All functions return promises and execute asynchronously. The `init` function is not required. 

# Usage

1. __[Query](#query)__
2. __[Collection](#collection)__
3. __[File Management](#file-management)__
4. __[Device](#device)__

# Query 

Class: ClearBladeAsync.Query()

This is an object to be used with the other async functions. The functions and examples are similar to the [ClearBlade Query Class](https://github.com/ClearBlade/native-libraries/blob/master/clearblade.md#query)

To instantiate the async query class just call:

~~~javascript
	var query = ClearBladeAsync.Query()
~~~

# Collection

Class: ClearBladeAsync.Collection(nameOrID)

This class allows for the execution of async ClearBlade collection functions and returns promises. The `init` function is not required.

To instantiate the async class just call:

~~~javascript
	var collection = ClearBladeAsync.Collection(nameorID)
~~~

## ClearBladeAsync.newCollection(name)

Creates a new ClearBlade collection.

* @param {string} name
* @returns {Promise}

~~~javascript
 ClearBladeAsync.newCollection("<COLLECTION_NAME>")
~~~

## ClearBladeAsync.Collection(nameOrID)

This function instantiates a Collection object.

 * @param {string} nameOrID
 * @returns {Collection}

Example

~~~javascript
	var collection  = ClearBladeAsync.Collection("<COLLECTION_NAME_OR_ID>");
	collection.fetch()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.fetch(query)

This function gets data from the collection.

 * @param {Query} _query
 * @returns {Promise} 
 
Example

~~~javascript
	var collection = ClearBladeAsync.Collection("<COLLECTION_NAME>")
   	var query = ClearBladeAsync.Query();
   	query.equalTo("<YOUR_COLUMN_OF_TYPE_STRING>", "<COLUMN_DATA>");
    collection.fetch(query)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.create(newItem)

This function creates a row in the collection.

 * {Object} newItem
 * @returns {Promise} 

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	var newItem = {
        column_name: '<COL_DATA>',
	}
	collection.create(newItem)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.update(query, changes)

 This function updates rows in the collection.

 * @param {Query} _query
 * {Object} changes
 * @returns {Promise}

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	var query = ClearBladeAsync.Query();
   	query.equalTo("<YOUR_COLUMN_OF_TYPE_STRING>", "<COLUMN_DATA>");
	var changes = {
        column: '<COL_DATA>',
	}
	async.update(query, changes)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.remove(query)

This function deletes rows from the collection.

 * @param {Query} _query
 * @returns {Promise}

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	var query = ClearBladeAsync.Query();
   	query.equalTo("<YOUR_COLUMN_OF_TYPE_STRING>", "<COLUMN_DATA>");
	collection.remove(query)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.columns()

This function gets all column metadata for the collection.

 * @returns {Promise}

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	collection.columns()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.addColumn(columnMeta)

This function adds a column to the collection.
 
 * {Object} columnMeta
 * {string} columnMeta.name
 * {string} columnMeta.type
 * @returns {Promise}

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	var columnMeta = 
	{
        name: '<COL_NAME>',
		type: '<COL_TYPE>',
	}
	collection.addColumn(columnMeta)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Collection.dropColumn(columnName)

This function removes a column to the collection.

 * {string} columnName
 * @returns {Promise}

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	columnName = '<COL_NAME>'
	collection.dropColumn(columnName)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~
## ClearBladeAsync.Collection.deleteCollection()

This function deletes the collection .

 * @returns {Promise}

Example

~~~javascript
	var collection = ClearBladeAsync.Collection("Name")
	collection.deleteCollection()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~


# FileSystem 

## ClearBladeAsync.Collection.createIndex(columnToIndex)

This function creates an index on a collection column. By default, a collection can be indexed on the `item_id` column. Users can index a collection on any column.

* @param {string} columnName
* @returns {Promise}

Example

~~~javascript
    var collection = ClearBladeAsync.Collection("<COLLECTION_NAME>")
    var columnToIndex = "myColumn"
    collection.createIndex(columnToIndex)
        .then(resp.success)
        .catch(function(reason){
            resp.error("caught: "+reason.message);
        })
~~~

## ClearBladeAsync.Collection.createUniqueIndex(columnToIndex)

This function creates a unique index on a collection column.

* @param {string} columnName
* @returns {Promise}

Example

~~~javascript
    var collection = ClearBladeAsync.Collection("<COLLECTION_NAME>")
    var columnToIndex = "myColumn"
    collection.createUniqueIndex(columnToIndex)
        .then(resp.success)
        .catch(function(reason){
            resp.error("caught: "+reason.message);
        })
~~~

 
## ClearBladeAsync.Collection.upsert(changes, conflictColumn)

This function performs an upsert operation on a collection. The `conflictColumn` must be either a primary key or have a unique index.

* @param {object} changes
* @param {string} conflictColumn
* @returns {Promise}

Example

~~~javascript
    var collection = ClearBladeAsync.Collection("<COLLECTION_NAME>")
    var conflictColumn = "myColumn"
    var changes = {
        "myColumn": "myUniqueId",
        "data": "someData"
    }
    collection.upsert(changes, conflictColumn)
        .then(resp.success)
        .catch(function(reason){
            resp.error("caught: "+reason.message);
~~~


Class: ClearBladeAsync.FS()

This class adds support for interacting with the sync’d filesystem. All file paths are relative to bucket root, except on an edge where you can use full paths.

To instantiate the async filesystem class just call:

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
~~~

## ClearBladeAsync.FS(deployment_name)

Represents a sync'd filesystem.

* @param {string} deployment_name
* @returns {FS}
Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	
~~~

## ClearBladeAsync.FS.readDir(path)

Reads the contents of a directory.

 * @param {string} path
 * @returns {Promise}- array of file names

Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	FS.readDir(path)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.FS.readFile(path,[encoding])

 This function reads the entire contents of a file.
 If encoding is not specified, contents will be a `UInt8Array`. If encoding is specified, contents will be a string.

 * @param {string} path
 * @param {string} [encoding]
 * @returns {Promise} -  file contents

Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	FS.readFile(path,'utf8')
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.FS.writeFile(path,data)

This function writes the given data to a file, replacing the file if it already exists.

 * @param {string} path
 * @param {string|Uint8Array} data
 * @returns {Promise}

Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	FS.writeFile(path,data)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.FS.renameFile(oldPath, newPath)

 This function renames the file path.

 * @param {string} oldPath
 * @param {string} newPath
 * @returns {Promise}

Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	FS.renameFile(oldPath, newPath)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.FS.copyFile(srcPath, dstPath)

 This function copies srcPath to dstPath. It overwrites dstPath if it already exists.

 * @param {string} srcPath
 * @param {string} dstPath
 * @returns {Promise}

Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	FS.copyFile(srcPath, dstPath)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.FS.deleteFile(path)

This function deletes the file at the specified path.

 * @param {string} path
 * @returns {Promise}

Example

~~~javascript
	var FS = ClearBladeAsync.FS('myDeployment')
	FS.deleteFile(path)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.FS.stat(path)

The promise is resolved with the `FileStats` object for the given path.

 * @param {string} path
 * @returns {Promise}

Example  

~~~javascript
var FS = ClearBladeAsync.FS('myDeployment')
FS.stat(path)
	.then(resp.success)
	.catch(function(reason){
		resp.error("caught: "+reason.message);
	})
~~~

# File Management 

Class: ClearBladeAsync.File()

This class adds support for interacting with a file in the sync’d filesystem.

To instantiate the async file class just call:

~~~javascript
	var fstats = ClearBladeAsync.File(deployment_name, path)
~~~

## ClearBladeAsync.File(deployment_name, path)

This function is useful for performing multiple operations on a single file.

 * @param {string} deployment_name
 * @param {string} path
 * @returns {File}

Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.stat()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.File.stat()

The promise is resolved with the `FileStats` object for the file.

 * @returns {Promise}

Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.stat()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.File.read([encoding])

This function reads the entire contents of the file.
If encoding is not specified, contents will be a UInt8Array.
If encoding is specified, contents will be a string.

 * @param {string} [encoding]
 * @returns {Promise} - file contents

Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.read("utf8")
		.then(function(logString){
        return logString.split('\n').slice(-100).join('\n');
        })
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.File.write(data)

This function writes the given data to the file, replacing the contents if it already exists.

 * @param {string|Uint8Array} data
 * @returns {Promise}

 Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.write(data)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.File.rename(newPath)
This function renames file to newPath.

 * @param {string} newPath
 * @returns {Promise}

Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.rename(newPath)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.File.copy(dstPath)

This function copies file to `dstPath`, overwriting dstPath if it already exists.

 * @param {string} dstPath
 * @returns {Promise}


Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.copy(dstPath)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.File.delete()

This function deletes the file.

 * @returns {Promise}

 Example

~~~javascript
	var file = ClearBladeAsync.File('myDeployment', 'sandbox/myFile.txt')
	file.delete()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

# Device

Class: ClearBladeAsync.Collection(nameOrID)

This class allows for the execution of async ClearBlade device operations in services. 

To instantiate the async class just call:

~~~javascript
	var Devices = ClearBladeAsync.Devices()
~~~

## ClearBladeAsync.Devices()

This function returns the list of devices in the system.

* @returns {Devices}

Example

~~~javascript
	var Devices  = ClearBladeAsync.Devices();
	Devices.fetch()
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Devices.create(info)

This function creates a new device.

* @param {Object} info - initial data describing the new device.
* @returns {Promise<Object>}

Example

~~~javascript
	var Devices  = ClearBladeAsync.Devices();
	var info = {
        name: '<DEVICE_NAME>',
	}
	Devices.create(info)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Devices.read(query)

This function fetches rows (devices) from the device table.

* @param {ClearBladeAsync.Query} query
* @returns {Promise<Object[]>}

Example

~~~javascript
	var Devices  = ClearBladeAsync.Devices();
	var query = ClearBladeAsync.Query()
	query.equalTo("<DEVICE_NAME>");
	Devices.read(query)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Devices.update(query, changes)

This function updates rows (devices) in the device table.

* @param {ClearBladeAsync.Query} query
* @param {Object} changes
* @returns {Promise<Object>}

Example

~~~javascript
	var Devices  = ClearBladeAsync.Devices();
	var query = ClearBladeAsync.Query();
	query.equalTo("<DEVICE_NAME>");
	var changes = {
        active_key: '<ACTIVE_KEY>',
	}
	Devices.update(query, changes)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~

## ClearBladeAsync.Devices.delete(query)

This function deletes rows (devices) from the device table.

* @param {ClearBladeAsync.Query} query
* @returns {Promise<Object>}

Example

~~~javascript
	var Devices  = ClearBladeAsync.Devices();
	var query = ClearBladeAsync.Query()
	query.equalTo("<DEVICE_NAME>");
	Devices.delete(query)
		.then(resp.success)
		.catch(function(reason){
			resp.error("caught: "+reason.message);
		})
~~~