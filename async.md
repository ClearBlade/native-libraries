The ClearBlade Async library works with the event loop to provide asynchronous ClearBlade functions. All functions return promises and execute asynchronously. The `init` function is not required. 

# Usage

1. __[Query](#query)__
2. __[Collection](#collection)__
3. __[Messaging](#messaging)__

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

# Messaging

This class allows for the execution of async ClearBlade messaging functions provide an MQTT client that will work asynchronously. You can connect to the internal ClearBlade broker or an external broker. 

## MQTT.Message(payload)

The message represents an MQTT message.

* @param {string} payload
* @constructor
* @property {string} payload
* @property {number} qos
* @property {boolean} retain
* @property {boolean} duplicate

Example

~~~javascript
	var msg = new MQTT.Message()
 	var msgData = JSON.parse(msg.payload);
~~~

## MQTT.Client(options)

The client represents an MQTT client connected to a broker.
If no options are specified, the client connects to the ClearBlade broker.

* @param {object} [options]
* @param {string} options.address
* @param {number} [options.port=1883] 
* @param {string} [options.username] 
* @param {string} [options.password]
* @param {string} [options.client_id={randomUUID}]
* @param {boolean} [options.use_tls=false]
* @param {object} [options.tls_config]
* @param {string} options.tls_config.client_cert
* @param {string} options.tls_config.client_key
* @param {string} options.tls_config.ca_cert
* @constructor

Example

~~~javascript
var client = new MQTT.Client();
var options = {
  address: 'platform.clearblade.com',
  port: 1883,
  username: "example@clearblade.com",
  password: "test",
  use_tls: false,
  tls_config: {
    ca_cert: ca_cert,
  },
}
~~~

## Client.subscribe(topic, onMessage)

This function subscribes to a topic in the broker and registers a callback function which is called when a message on that topic is received.

* @param {string} topic
* @param {function(topic: string, message: Message)} onMessage
* @returns {Promise}

~~~ javascript
var client = new MQTT.Client();
function myStreamService(req, resp){
  client.subscribe("incoming/data/topic", data)
    .catch(function(reason){
      resp.error("failed to subscribe: "+reason.message)
    })
~~~

## Client.publish(topic, payload, qos, retain)

This function allows publish to send a MQTT message to the broker.

* publish sends an MQTT message to the broker.
* @param {string} topic
* @param {string|Message} payload
* @param {number} [qos]
* @param {boolean} [retain]
* @returns {Promise}

~~~ javascript
var client = new MQTT.Client();
function checkDataLimits(topic, msg) {
  var msgData = JSON.parse(msg.payload);
  if (msgData.data > 90) {
    var alert = "Data on device " + msgData.deviceID + " too high (" + msgData.data + ")";
    client.publish("alert/topic", alert)
      .catch(function(reason){
        log("failed to publish alert \""+alert+"\": "+reason.message)
      })
  }
  ~~~