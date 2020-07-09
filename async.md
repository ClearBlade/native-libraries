The ClearBlade Async library works with the event loop to provide asynchronous ClearBlade functions. All functions return promises and execute asynchronously. The `init` function is not required. 

# Usage

1. __[Query](#query)__
2. __[Collection](#collection)__

# Query 

Class: ClearBladeAsync.Query(options)

This class allows for the execution of async ClearBlade query functions and returns promises. The functions and examples are similar to the [ClearBlade Query Class](https://github.com/ClearBlade/native-libraries/blob/master/clearblade.md#query)

To instantiate the async query class just call:

~~~javascript
	var query = ClearBladeAsync.Query("Activity")
~~~

# Collection

Class: ClearBladeAsync.Collection(options)

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

This function returns collection query data.

 * @param {string} nameOrID
 * @returns {Collection}

Example

~~~javascript
// Use a string parameter for the Collection object. The string can be either the collection name or collection id
	var collection  = ClearBladeAsync.Collection({collectionName:"<COLLECTION_NAME>");
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

## ClearBladeAsync.Collection.fetch(query)

This function gets data from the collection.

 * @param {Query} _query
 * @returns {Promise} 

## ClearBladeAsync.Collection.create(newItem)

This function creates a row in the collection.

 * {Object} newItem
 * {string} newItem.column
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
 * {string} changes.column
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
