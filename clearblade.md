The ClearBlade library provides all the methods necessary for interacting with the ClearBlade Platform and its resources from within a code service.

# Usage

1. __[Initialization](#init)__
1. __[Device](#device)__
1. __[Cache](#cache)__
1. __[Query](#query)__
1. __[Collection](#collection)__
2. __[Database](#database)__
1. __[Messaging](#messaging)__
1. __[Timers](#timer)__
1. __[Triggers](#trigger)__
1. __[Edge](#edge)__

# Overview

ClearBlade.js Library enables users to:

>1. Query a local or remote data collection
>2. Listen to an MQTT topic and process the payload
>3. Store data into a collection
>3. Modify devices attributes
>4. Create a timer or trigger
>5. Register new users
>6. Log users in or out
>1. Delete users


# ClearBlade

The available methods for the ClearBlade class and examples of how to use them are listed below:


## init

This method sets up the ClearBlade object with a few configuration values necessary for subsequent operations against the ClearBlade Platform.

Note: You must init before accessing any ClearBlade resources within your system

Passing the request into the init function is the simplest and most performant way to establish an authorized instance of the ClearBlade object.  The ClearBlade object will inherit the permissions associated with the calling user.

Recommended:

~~~javascript
	//In this example the passed in information on the request object is used to init the ClearBlade object
	ClearBlade.init({request:req});
~~~

To establish a new session for the ClearBlade object; the `options` object contains all the parameters passed into the `init` method. It can contain the following keys / value pairs:

Mandatory Parameters:

>- systemKey - the system key of the system you would like to access (available in req object)

>- systemSecret - the system secret of the system you would like to access (available in req object)


Optional Parameters:

>- email - the email of the user you would like to login/register for subsequent operations
>- password - the password of the user you would like to login/register for subsequent operations
>- registerUser - boolean that signifies that the email and password params should be used to register a new user with the platform. The newly registered user will be logged in
>- useUser - an object in the form of {"authToken": "userToken"} used to set the user for any ensuing operations
>- callback - a function that takes a boolean for error checking and a JSON object. Must be supplied if any of the following options are supplied: email, password


Basic Example:
~~~javascript
		//this example simply initializes the ClearBlade object with the desired system.
		ClearBlade.init({request:req});
~~~

Authentication Example:

~~~javascript
		//this example uses a users email and password to log in
		ClearBlade.init({
			systemKey: req.systemKey,
			systemSecret: req.systemSecret,
			email: "<USER_EMAIL>",
			password: "<PASSWORD>",
			callback: function(err, body) {
				if(err) {
					resp.error("initialization error " + JSON.stringify(body));
				} else {
					resp.success(body);
				}
			}
		});
~~~

Note: Invalid credentials (bad username or password) leads to the #init methods to throw an error. You must catch this error using a try catch block.

Registration Example:
~~~javascript
		//this example uses an email and password to register a new user with the platform
		ClearBlade.init({
			systemKey: req.systemKey,
			systemSecret: req.systemSecret,
			registerUser: true,
			email: "<USER_EMAIL>",
			password: "<USER_PASSWORD>",
			callback: function(err, body) {
				if(err) {
					resp.error("Registration Error " + JSON.stringify(body));
				} else {
					resp.success(body); //Returns a user token
				}
			}
		});
~~~
Pre-authed example:
~~~javascript
		// Uses an email and auth token of a pre-authorized user - no need for a callback
		ClearBlade.init({
			systemKey: req.systemKey,
			systemSecret: req.systemSecret,
			useUser: {
				email: req.userid
				authToken: req.userToken
			}
		});
~~~

## ClearBlade.setUser(email, authToken)

This method sets the user to be used for all operations against the ClearBlade Platform.

Example:
~~~javascript
	ClearBlade.init({
		systemKey: req.systemKey,
		systemSecret: req.systemSecret
	});
	ClearBlade.setUser("userEmail", "<PASSWORD>");
~~~

## ClearBlade.loginAnon(callback)

This method will log into the ClearBlade platform anonymously and use the token for subsequent requests.

Example:
~~~javascript
	ClearBlade.init({
		systemKey: req.systemKey,
		systemSecret: req.systemSecret
	});
	ClearBlade.loginAnon(function(err, body) {
		if(err) {
			resp.error("Anonymous login failure: " + JSON.stringify(body));
		} else {
			resp.success(body); // `body` object contains Auth Token
		}
	});
~~~

## ClearBlade.registerUser(email, password, callback)

This method will register a user against the ClearBlade Platform with the supplied email and password and then log them in.

Example:
~~~javascript
	ClearBlade.init({
		systemKey: req.systemKey,
		systemSecret: req.systemSecret
	});
	ClearBlade.registerUser("<USER_EMAIL>", "<PASSWORD>", function(err, body) {
		if(err) {
			resp.error("Register user failure: " + JSON.stringify(body));
		} else {
			resp.success(body); // `body` object contains Auth Token
		}
	});
~~~

## ClearBlade.isCurrentUserAuthenticated(callback)

This method is used to check and see if current users authentication token is still valid.

Example:
~~~javascript
	ClearBlade.init({
		systemKey: req.systemKey,
		systemSecret: req.systemSecret
	});
	ClearBlade.isCurrentUserAuthenticated(function(err, body) {
		if(err) {
			resp.error("Auth check fail: " + JSON.stringify(body));
		} else {
			resp.success(body); // `body` contains a boolean value
		}
	});
~~~

## ClearBlade.loginUser(email, password, callback)

This method is used to log a user into the ClearBlade Platform before making requests.

Example:
~~~javascript
	ClearBlade.init({
		systemKey: req.systemKey,
		systemSecret: req.systemSecret
	});
	ClearBlade.loginUser("<USER_EMAIL>", "<USER_PASSWORD>", function(err, body) {
		if(err) {
			resp.error("User login failure: " + JSON.stringify(body));
		} else {
			resp.success(body); // Return an auth token
		}
	});
~~~

## ClearBlade.logoutUser(callback)

This method is used to log the current user out from the platform.

Example:
~~~javascript
	ClearBlade.init({
		systemKey: req.systemKey,
		systemSecret: req.systemSecret
	});
	ClearBlade.loginUser("<USER_EMAIL>", "<PASSWORD>", function(err, body) {
		if(err) {
			resp.error("user login fail " + JSON.stringify(body));
		} else {
			ClearBlade.logoutUser(function(e, res) {
				if(e) {
					resp.error("user logout fail " + JSON.stringify(body));
				} else {
					resp.success(res); // Returns {"authToken":null,"email":null}
				}
			});
		}
	});
~~~

## ClearBlade.deleteUser(email, callback)

This method is used to delete a user by email.

Example:

```javascript
ClearBlade.init(req);
ClearBlade.deleteUser("<USER_EMAIL>", function(err, body) {
	if(err) {
		resp.error("user deletion failure: " + JSON.stringify(body));
	} else {
		resp.success(body);
	}
});
```

## ClearBlade.deleteUsers(listOfEmails, callback)

This method is used to delete multiple users by email.

Example:

```javascript
ClearBlade.init(req);
emailsToDelete = ["<USER_EMAIL_ONE>", "<USER_EMAIL_TWO>", "<USER_EMAIL_THREE>"];
ClearBlade.deleteUsers(emailsToDelete, function(err, body) {
	if(err) {
		resp.error("user deletion failure: " + JSON.stringify(body));
	} else {
		resp.success(body);
	}
});
```

## Device

ClearBlade.getAllDevicesForSystem(callback)

This method is used to retrieve all devices from the Devices Auth table. If successful, the response will contain an array of every device defined within a System. Each element in the array will be a JSON object whose attributes correspond to the columns in the Devices table, including any custom columns.

~~~json
[
  {
    "__HostId__": "20b6b826-39f3-4a23-9559-62099d08c42a",
    "active_key": "1234567890",
    "allow_certificate_auth": true,
    "allow_key_auth": true,
    "certificate": "",
    "created_date": "2017-04-04T14:39:32-05:00",
    "customcolumn1": "",
    "customcolumn2": "",
    "description": "",
    "device_key": "b0889f8e0b86df81dff589e6840d :: BLE Gateway RPi",
    "enabled": true,
    "keys": "",
    "last_active_date": "2017-04-06T10:43:53-05:00",
    "name": "BLE Gateway RPi",
    "state": "",
    "system_key": "b0889f8e0b86df81dff589e6840d",
    "type": "BLE Gateway"
  }
]
~~~

Example:
~~~javascript
	ClearBlade.init({request: req});

	ClearBlade.getAllDevicesForSystem(function(err, data) {
		if(err){
			resp.error("Unable to get all devices: " + JSON.stringify(data))
		}
		resp.success(data)
	});
~~~  

## ClearBlade.getDeviceByName(name, callback)  

This method is used to retrieve a device from the Devices Auth table. If successful, the response will contain a JSON object representing the requested device. The attributes in the JSON object correspond to the columns in the Devices table, including any custom columns.

~~~json
{
  "__HostId__": "20b6b826-39f3-4a23-9559-62099d08c42a",
  "active_key": "1234567890",
  "allow_certificate_auth": true,
  "allow_key_auth": true,
  "certificate": "",
  "created_date": "2017-04-04T14:39:32-05:00",
  "customcolumn1": "",
  "customcolumn2": "",
  "description": "",
  "device_key": "b0889f8e0b86df81dff589e6840d :: BLE Gateway RPi",
  "enabled": true,
  "keys": "",
  "last_active_date": "2017-04-06T10:43:53-05:00",
  "name": "BLE Gateway RPi",
  "state": "",
  "system_key": "b0889f8e0b86df81dff589e6840d",
  "type": "BLE Gateway"
}
~~~

If unsuccessful, an error will be returned in the __data__ parameter passed to the callback function.
~~~json
{
	"error": {
		"id": "00000000-0000-0000-0000-000000000000",
		"code": 1102,
		"level": 1,
		"category": "Devices",
		"message": "Internal Server Error",
		"detail": "Device 'test' does not exist",
		"lowLevelError": {}
	}
}
~~~

Example:
~~~javascript
	ClearBlade.init({request: req});
	ClearBlade.getDeviceByName("Test Device", function(err, data) {
		if(err){
			resp.error("Unable to get device: " + JSON.stringify(data))
		} else {
			resp.success(data)
		}
	});
~~~

## ClearBlade.createDevice(name, object, causeTrigger, callback)  

This method is used to create a new device in the Devices Auth table. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns, and will be used to initialize the contents of the respective column. If successful, the __data__ parameter passed to the callback will contain a json object representing the created device.

~~~json
{
  "__HostId__": "172d7a09-c8c4-449c-87b6-958d6dc04941",
  "allow_certificate_auth": true,
  "allow_key_auth": true,
  "causeTrigger": true,
  "created_date": "2017-04-06T11:34:31-05:00",
  "device_key": "b0889f8e0b86df81dff589e6840d :: Test Device",
  "enabled": true,
  "last_active_date": "2017-04-06T11:34:31-05:00",
  "name": "Test Device",
  "state": "",
  "system_key": "b0889f8e0b86df81dff589e6840d",
  "type": ""
}
~~~  

If unsuccessful, an error will be returned in the __data__ parameter passed to the callback function.
~~~json
{
	"error": {
		"id":"00000000-0000-0000-0000-000000000000",
		"code":1103,
		"level":1,
		"category":"Devices",
		"message":"Internal Server Error",
		"detail":"Device 'Test Device' already exists in system 'b0889f8e0b86df81dff589e6840d'",
		"lowLevelError":{}
	}
}
~~~

Example:  
~~~javascript
	ClearBlade.init({request: req});

	// Default is true, so device table changes can trigger code services
	var DEVICE_TRIGGER_ENABLED = true

	var device = {
		name: "<DEVICE_NAME>",
		active_key: "example_active_key",
		type: "",
		state: "",
		enabled: true,
		allow_key_auth: true,
		allow_certificate_auth: true
	};

	ClearBlade.createDevice(device.name, device, DEVICE_TRIGGER_ENABLED, function(err, data) {
		if(err){
			resp.error("Unable to create device: " + JSON.stringify(data))
		}
		resp.success(data);
	});
~~~  

## ClearBlade.updateDevice(name, object, causeTrigger, callback)  

This method is used to update a device in the Devices Auth table. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns, and will be used to initialize the contents of the respective column. If successful, the __data__ parameter passed to the callback will contain a __success__ attribute.

{{< warning title="Heads Up!" >}}
Once a device has been created, you CANNOT change its name. Do not specify the __name__ attribute when updating a device.
{{< /warning >}}

~~~json
{
  "success": true
}
~~~  

If unsuccessful, an error will be returned in the __data__ parameter passed to the callback function.
~~~json
{
	"error":{
		"id":"00000000-0000-0000-0000-000000000000",
		"code":1106,
		"level":1,
		"category":"Devices",
		"message":"Unable to validate arguments for device",
		"detail":"Not allowed to update field 'name'",
		"lowLevelError":{}
	}
}
~~~

Example:  
~~~javascript
    ClearBlade.init({request: req});

    var deviceNameToUpdate = "<DEVICE_NAME>"

    // Default is true, so device table changes can trigger code services
	var DEVICE_TRIGGER_ENABLED = true

	var deviceUpdates = {
		active_key: "example_active_key",
		type: "",
		state: "",
		enabled: true,
		allow_key_auth: false,
		allow_certificate_auth: false
	};

	ClearBlade.updateDevice(deviceNameToUpdate, deviceUpdates, DEVICE_TRIGGER_ENABLED, function(err, data) {
		if(err) {
			resp.error("Unable to update device: " + JSON.stringify(data))
		}

		resp.success(data);
	});
~~~

## ClearBlade.deleteDevice(name, causeTrigger, callback)  

This method is used to create a device in the Devices Auth table. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns, and will be used to initialize the contents of the respective column. If successful, the __data__ parameter passed to the callback will contain a __success__ attribute.

~~~json
{
  "success": true
}
~~~  

If unsuccessful, an error will be returned in the __data__ parameter passed to the callback function.
~~~json
{
	"error":{
		"id":"00000000-0000-0000-0000-000000000000",
		"code":1104,
		"level":1,
		"category":"Devices",
		"message":"Internal Server Error",
		"detail":"Cannot delete: Device 'Test Device' does not exist",
		"lowLevelError":{}
	}
}
~~~

Example:  
~~~javascript
    ClearBlade.init({request: req});

	// Default is true, so device table changes can trigger code services
	var DEVICE_TRIGGER_ENABLED = true

	ClearBlade.deleteDevice("<DEVICE_NAME>", DEVICE_TRIGGER_ENABLED, function(err, data) {
		if(err) {
			resp.error("Unable to update device: " + JSON.stringify(data))
		}

		resp.success(data);
	});
~~~

## ClearBlade.connectedDevices(callback)

This method is used to retrieve information about the devices currently connected to the platform via MQTT.
The response is a map from the device names to a list of connection details.
The connection details include the clientID the device connected with, and the time it started the connection (UTC).

~~~json
{
   "deviceNameA":[
       {
           "client_id": "DhkVG42mlBQ7DkZSvb5d",
           "time_connected": "2019-12-11T20:48:10.875786Z"
       }
   ],
   "deviceNameB":[
       {
           "client_id": "8I27kjNaI6290Npp64hl",
           "time_connected": "2019-12-13T17:53:09.5778599Z"
       }
   ]
}
~~~

Example:
~~~javascript
    ClearBlade.init({request: req});

    ClearBlade.connectedDevices(function(err, data) {
        if(err){
            resp.error("Unable to get connected devices: " + JSON.stringify(data))
        }
        resp.success(data)
    });
~~~

## ClearBlade.deviceConnections(deviceName, callback)

This method is used to retrieve information about a single device currently connected to the platform via MQTT.
The response is a map containing all the usual device details, as well as a list of connection details for each active connection.
The connection details include the clientID the device connected with, and the time it started the connection (UTC).

~~~json
{
    "allow_certificate_auth": false,
    "allow_key_auth": true,
    "certificate": null,
    "connections": [
        {
            "client_id": "8I27kjNaI6290Npp64hl",
            "time_connected": "2019-12-13T17:53:09.5778599Z"
        }
    ],
    "created_date": 1576095410,
    "description": null,
    "device_key": "e4ba8adf0beeea9dcc909dafaeab01 :: deviceNameB",
    "enabled": true,
    "has_keys": false,
    "last_active_date": 1576095410,
    "name": "deviceNameB",
    "state": "",
    "system_key": "e4ba8adf0beeea9dcc909dafaeab01",
    "type": ""
}
~~~

Example:
~~~javascript
    ClearBlade.init({request: req});

    ClearBlade.deviceConnections("deviceNameB", function(err, data) {
        if(err){
            resp.error("Unable to get device connections: " + JSON.stringify(data))
        }
        resp.success(data)
    });
~~~

## ClearBlade.connectedDeviceCount(callback)

This method is used to retrieve the number of devices connected to the platform via MQTT.
The response contains three keys: unique\_device\_connections, total\_device\_connections, and total\_devices.
Total devices is simply a count of the devices in your device table.
Unique device connections is how many of those devices have at least one active MQTT connection to the platform.
Total device connections is how many active MQTT connections to the platform are being made by your devices.

~~~json
{
    "unique_device_connections": 2,
    "total_device_connections": 5,
    "total_devices": 3
}
~~~

Example:
~~~javascript
    ClearBlade.init({request: req});

    ClearBlade.connectedDeviceCount(function(err, data) {
        if(err){
            resp.error("Unable to count connected devices: " + JSON.stringify(data))
        }
        resp.success(data)
    });
~~~

## Cache

Class: ClearBlade.Cache(cacheName);

Caches can be shared across services and make data access faster as compared to database operations. The ClearBlade.Cache object is used perform cache operations like GET, GETALL, SET, SETNX, SETMULTIPLE, DELETE and FLUSH. __Please note that setting `undefined` values in the cache cause abnormal behavior.__  

To instantiate the cache object you need the name of your cache.

~~~javascript
	var cache = ClearBlade.Cache('<CACHE_NAME>');
~~~

### Cache Callback

The following callback function can be used for all cache callbacks.

@callback cbCallback:  
@param {boolean} err - Is true if there is an error  
@param {string} data - data or error description in case of an error

## Cache.set(key, value, callback)
Sets data in the cache. Requires that the Cache object was initialized with a cache name. On success, this returns a string "Set done".

* @param {string} key - Key in cache
* @param {string|number|object|array|boolean|null} value - Data to be stored in the cache
* @param {function} callback - callback that returns error or success messages

~~~~javascript
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("Set error : " + JSON.stringify(data));
   	    } else {
   	    	// Success. Do something
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
	var data = {"name": "Bob", "age": 70};
   	cache.set("myKey", data, callback);
~~~~

## Cache.setnx(key, value, callback)
Sets data in the cache if it does not exist. Requires that the Cache object was initialized with a cache name. On success, this returns a boolean string. Returns boolean __true__ if it set the data in the cache. Returns boolean __false__ if the data already exists

* @param {string} key - Key in cache
* @param {string|number|object|array|boolean|null} value - Data to be stored in the cache
* @param {function} callback - callback that returns error or success messages

~~~~javascript
   	var callback = function (err, wasSet) {
   	    if (err) {
   	    	resp.error("SetNX error : " + JSON.stringify(data));
   	    } else {
   	    	if(wasSet === true) {
			// Data was set. Do something
		} else {
			// Data already exists. Do something else
		}
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
	var data = {"name": "Bob", "age": 70};
   	cache.setnx("myKey", data, callback);
~~~~

## Cache.setMultiple(data, callback)
Sets multiple entries in the cache at once. Requires that the Cache object was initialized with a cache name. Requires that the data passed to this function is a JSON object. On success, returns a string "SetMultiple done"

* @param {object} data - Multiple data entries to be stored in the cache
* @param {function} callback - callback that returns error or success messages

~~~~javascript
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("SetMultiple error : " + JSON.stringify(data));
   	    } else {
   	    	// Success. Do something
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
	var data = {"abc": {"x": 12.34, "y": 56.89}, "def": {"x": 33.58, "y": 76.34}, "xyz": {"x": 78.45, "y": 99.67}};
   	cache.setMultiple(data, callback);
~~~~

## Cache.get(key, callback)    
Gets data corresponding to the key from the cache. Requires that the Cache object was initialized with a cache name. Two possible return values on success:       
	- If key not found, returns undefined.   
	- If key found, returns data. __(Type of data is same as set in the cache)__.   

* @param {string} key - Key in cache
* @param {function} callback - callback that returns error or data corresponding to the key

~~~~javascript
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("Get error : " + JSON.stringify(data));
   	    } else {
   	    	resp.success(JSON.stringify(data));
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
   	cache.get("myKey", callback);
~~~~

## Cache.getAll(callback)
Gets all data from the cache. Requires that the Cache object was initialized with a cache name. On success, returns all data as a JSON object with key/value pairs. If the cache is empty, an empty JSON object is returned

* @param {function} callback - callback that returns error or all data from the cache

~~~~javascript
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("GetAll error : " + JSON.stringify(data));
   	    } else {
   	    	resp.success(JSON.stringify(data));
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
   	cache.getAll(callback);
~~~~

## Cache.delete(key, callback)
Deletes data corresponding to the key from the cache. Requires that the Cache object was initialized with a cache name. On success, returns a string "Delete done"

* @param {string} key - Key in cache
* @param {function} callback - callback that returns error or success messages

~~~~javascript
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("Delete error : " + JSON.stringify(data));
   	    } else {
   	    	// Success. Do something
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
   	cache.delete("myKey", callback);
~~~~

## Cache.flush(callback)
Deletes all data from the cache. Requires that the Cache object was initialized with a cache name. On success, returns a string "Flush done"

* @param {function} callback - callback that returns error or success messages

~~~~javascript
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("Flush error : " + JSON.stringify(data));
   	    } else {
   	    	// Success. Do something
   	    }
   	};
   	var cache = ClearBlade.Cache('<CACHE_NAME>');
   	cache.flush(callback);
~~~~

## Query

Class: ClearBlade.Query(options);

Queries make it possible to retrieve and update data in collections based on criteria.  The ClearBlade.Query object is used to rapidly create the query and ultimately perform possible fetch, update, create, and delete actions.  

{{< note title="Query Default Page Size" >}}
Query will return 100 results by default, unless specified by Query.setPage
{{< /note >}}

Dive into the Query concept: [Query: Learn More](/1-platform_concepts/data/query/)

To instantiate a query object you have three options:

Here are the mutually exclusive values for the object:

>- collectionName (string) - the name of the collection you want to access
>- collectionID (string) - the ID of the collection you want to access
>- collection (string) - the ID of the collection you want to access

1) Supply an object containing the <COLLECTION_NAME>
~~~javascript
		var query = ClearBlade.Query({collectionName:"<COLLECTION_NAME>"});
~~~
Alternatively, the collectionID key can be used with the <COLLECTION_ID> value
~~~javascript
		var query = ClearBlade.Query({collectionID:"<COLLECTION_ID>"});
~~~
Additionally, the <COLLECTION_ID> can be used
~~~javascript
		var query = ClearBlade.Query("<COLLECTION_ID>");
~~~

3) Supply nothing (this only applies when using the methods on the ClearBlade.Collection or ClearBlade.User classes to make requests against data)
~~~javascript
		var query = ClearBlade.Query();
~~~

## Query.columns([string])

This method is used to limit the columns returned by the query to the specified array of strings.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.columns(["<COLUMN_NAME_1>", "<COLUMN_NAME_2>"]);
~~~~

## Query.ascending(field)
This method is used to sort the response from the query by a particular field in ascending order.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.ascending("<COLUMN_NAME_1>");
	// if applicable, apply second sort for entries with same value for column 'colName'
	query.ascending("<COLUMN_NAME_2>");
~~~~

## Query.descending(field)
This method is used to sort the response from the query by a particular field in descending order.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.descending("<COLUMN_NAME_1>");
	// if applicable, apply second sort for entries with same value for column 'colName'
	query.descending("<COLUMN_NAME_2>");
~~~~

{{< warning title="Please Note: Timestamps" >}}
ClearBlade timestamp types are in ms (milliseconds), not ns (nanoseconds). If you use the JavaScript Date Object, you will need to /= 1000.
{{< /warning >}}

## Query.equalTo(field, value)
This method is used to filter the data by using an equality operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});

	// Column Type: String
	query.equalTo("<COLUMN_NAME_1>", "mustEqualThis");

	// Column Type: Boolean
	query.equalTo("<COLUMN_NAME_1>", true)     // this is valid
	query.equalTo("<COLUMN_NAME_2>", "true") 	// this is valid

	// Column Types: Int, BigInt, Float, Double
	query.equalTo("<COLUMN_NAME_2>", 100) 		// this is valid
	query.equalTo("<COLUMN_NAME_2>", "100") 	// !! this is NOT valid !!
~~~~

## Query.greaterThan(field, value)
This method is used to filter the data by using a greater than operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.greaterThan("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.greaterThanEqualTo(field, value)
This method is used to filter the data by using a greater than equal to operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.greaterThanEqualTo("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.lessThan(field, value)
This method is used to filter the data by using a less than operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.lessThan("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.lessThanEqualTo(field, value)
This method is used to filter the data by using a less than equal to operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.lessThanEqualTo("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.notEqualTo(field, value)
This method is used to filter the data by using a not equal to operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.notEqualTo("YOUR_COLUMN_OF_TYPE_STRING", "hello");
~~~~

## Query.rawQuery(rawQueryString)
This method is used to supply a raw query string to the query object.  
__NOTE:__ This is currently only supported for MongoDB. Also note that if you use this, it will override
all other query operations like equalTo, lessThan, greaterThan etc. You can still use ___Query.ascending___,
___Query.descending___ or ___Query.setPage___ to set sort order or paging.   
~~~~javascript
	// MongoDB query example
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	var rawQueryJson = {"age": {"$exists": true, "$lt": 45}}
	query.rawQuery(JSON.stringify(rawQueryJson));
~~~~

## Query.matches(field, value)
This method is used to filter the data by using a regular expression against a given column.

~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.matches("YOUR_COLUMN_OF_TYPE_STRING", ".*mustContainThis.*");
~~~~
{{< note title="Regex Flags" >}}
If regex flags are needed, the regular expression should be preceded by __(?_flags_)__. For example, a regular expression for a case insenstive match would resemble __(?i).\*This is a case insensitive match.\*__
{{< /note >}}

{{< note title="Regex Parameter Type" >}}
The second parameter is a string literal, not a JavaScript Regex object
{{< /note >}}

{{< note title="Leading and Trailing Slashes" >}}
You do not need to surround your regex with __/__. Example: passing in __.\*zard__ as a second parameter will be executed as __/.*zard/__
{{< /note >}}

{{< warning title="URL Encoding" >}}
Many characters used in regular expressions have special meanings in the context of URL's and HTTP payloads. If you receive a _fetch error : "unexpected end of JSON input"_ error, you will need to URL encode your regular expression.

~~~~javascript
	query.matches("blah_col", ".+?%lowercase.*$");
~~~~
{{< /warning >}}

~~~~javascript
	/*
	* Let's assume we have a collection named "PokémonCollection"
	* This collection has two columns: column1 of type string, column2 of type string
	* There is a single row: { column1:"charizard", column2:"holographic"}
	*/
	var query = ClearBlade.Query({collectionName: "PokémonCollection"});
	// the first parameter is the column name, in this case "column1"
	// the second parameter is a string literal
	query.matches("column1", "(?i).*zard");
	query.fetch(function(err, data){
		// the second parameter, "(?i).*zard", will be executed as /(?i).*zard/
		// and will match any column value that ends with "zard" (ignoring case)
		//
		// The single row { column1:"charizard", column2:"holographic"} will match and be returned

	})
~~~~

## Query.setPage(pageSize, pageNum)

The default paging is page 1 with 100 results.  

This method is used to set the desired page and page size for the request.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.setPage(25, 3);
~~~~

You can set the page number to 0 to retrieve all results.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.setPage(0, 0);
~~~~

## Query.or(query)
chains an existing query object to the Query object with or on the same column - "title"
~~~~javascript
   // Assume your collection's name is "Activity"
	var query1 = ClearBlade.Query({collectionName: "Activity"})
	var query2 = ClearBlade.Query({collectionName: "Activity"})

	query1.equalTo('title','Engineer')
	query2.equalTo('title','Manager')

	// If an entry has 'Engineer' or 'Manager' in 'title' column
	// they will be fetched with `finalQuery`
	var finalQuery = query1.or(query2)
~~~~

chains an existing query object to the Query object with or on different columns - "title", "city"

~~~~javascript
	// Assume your collection's name is "Activity"
	var query1 = ClearBlade.Query({collectionName: "Activity"})
	var query2 = ClearBlade.Query({collectionName: "Activity"})

	query1.equalTo('title','Engineer')
	query2.equalTo('city','Austin')
	// If an entry has 'Engineer' in 'title' column
	// or 'Austin' in the 'city' column
	// they will be fetched with `finalQuery`
	var finalQuery = query1.or(query2)
~~~~

## Query.fetch(callback)
Reqests an item or a set of items from the query. Requires that the Query object was initialized with a collection.

~~~~javascript
	//@param {function} callback - Supplies processing for what to do with the data that is returned from the collection
   	var callback = function (err, data) {
   	    if (err) {
   	    	resp.error("fetch error : " + JSON.stringify(data));
   	    } else {
   	    	resp.success(data);
   	    }
   	};
   	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
   	query.fetch(callback);
~~~~

## Query.update(changes, callback)

Updates an existing item or set of items. Requires that a collection was set when the Query was initialized.


**Example:**
~~~~javascript
	// @param {Object} changes - Object representing the attributes that you want changed
	// @param {function} callback - Function that handles the response of the server
    //This example assumes a collection of items that have the columns name and age.
    var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
    query.equalTo('name', 'John');
    var changes = {
        age: 23
    };
    var callback = function (err, data) {
        if (err) {
        	resp.error("update error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    query.update(changes, callback);
    //sets John's age to 23
~~~~

## Query.remove(callback)
Removes an existing item or set of items.  Requires that a collection was set when the Query was initialized.
**Example:**
~~~~javascript
	//@param {function} callback Function that handles the response from the server
    var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
    //This example contains the item with 'name' column entry is 'John'
    query.equalTo('name', 'John');
    var callback = function (err, data) {
        if (err) {
        	resp.error("removal error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    query.remove(callback);
    //removes every item whose 'name' attribute is equal to 'John'
~~~~

## Collection

Class: ClearBlade.Collection(options)

To instantiate a collection object you can either supply an object containing <COLLECTION_NAME>, <COLLECTION_ID> or a string for the collectionID. Here are the mutually exclusive values for the object:

* **collectionName** (string) - the name of the collection you want to access
* **collectionID** (string) - the ID of the collection you want to access
* **collection** (string) - the ID of the collection you want to access

~~~~javascript
	//With collectionName key and value:
	var collection = ClearBlade.Collection({collectionName: "<COLLECTION_NAME>"});
~~~~
~~~~javascript
	//With collectionID key and value
	var collection = ClearBlade.Collection({collectionID:"<COLLECTION_ID>"});
~~~~
~~~~javascript
	//With collectionID value
	var collection = ClearBlade.Collection("<COLLECTION_ID>");
~~~~

The available methods for the Collection class and examples of how to use them are listed below:

## Collection.fetch(query, callback)

Reqests an item or a set of items from the collection.

* @param {Query} _query Used to request a specific item or subset of items from the collection on the server. Optional.
* @param {function} callback Supplies processing for what to do with the data that is returned from the collection

Basic Example:

~~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("fetch error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var col = ClearBlade.Collection({collectionName:"<COLLECTION_NAME>"});
    col.fetch(callback);
~~~~
Example with Query:
~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("fetch error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var col = ClearBlade.Collection({collectionName:"<COLLECTION_NAME>"});
   	var query = ClearBlade.Query();
   	query.equalTo("YOUR_COLUMN_OF_TYPE_STRING", "hello");
    col.fetch(query, callback);
~~~

## Collection.create(newItem, callback)

Creates a new item in the collection and returns the created item to the callback

* @param {Object} newItem - An object that represents an item that you want to add to the collection
* @param {function} callback - Supplies processing for what to do with the data that is returned from the collection

**Example:**
~~~javascript
   	//This example assumes a collection of items that have the columns: name, height, and age.
    var newPerson = {
        name: 'Jim',
        height: 70,
        age: 32
    };
    var callback = function (err, data) {
        if (err) {
        	resp.error("creation error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };
    var col = ClearBlade.Collection( {collectionName: "<COLLECTION_NAME>" } );
    col.create(newPerson, callback);
    //this inserts the the newPerson item into the collection that col represents
~~~

## Collection.update(_query, changes, callback)

Updates an existing item or set of items

   * @param {Query} _query Query object to denote which items or set of Items will be changed
   * @param {Object} changes Object representing the attributes that you want changed
   * @param {function} callback Function that handles the response of the server


**Example:**

~~~javascript
    //This example assumes a collection of items that have the columns name and age.
    var query = ClearBlade.Query();
    query.equalTo('name', 'John');
    var changes = {
        age: 23
    };
    var callback = function (err, data) {
        if (err) {
        resp.error("update error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var col = ClearBlade.Collection({collectionName:"<COLLECTION_NAME>");
    col.update(query, changes, callback);
    //sets John's age to 23

~~~

## Collection.remove(_query, callback)

Removes an item or set of items from the specified collection

* @param {Query} _query Query object that used to define what item or set of items to remove
* @param {function} callback Function that handles the response from the server

**Example:**
~~~javascript
    //This example assumes that you have a collection with the item whose 'name' attribute is 'John'
    var query = ClearBlade.Query();
    query.equalTo('name', 'John');
    var callback = function (err, data) {
        if (err) {
        	resp.error("update error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var col = ClearBlade.Collection({collectionName: "<COLLECTION_NAME>"});
    col.remove(query, callback);
    //removes every item whose 'name' attribute is equal to 'John'
~~~
## Collection.addColumn(options, callback)

Adds a column to a specified collection.

* @param {object} options- Define the columns to be added.
* @param {function} callback - Function that handles the response from the server

~~~javascript
	ClearBlade.init({ request: req });
	var callback = function (err, data) {
		if (err) {
			resp.error("addColumn error : " + JSON.stringify(data));
		} else {
			resp.success(data);
		};
	}
	var addColumnOptions = {
		"name": "cool",
		"type": "string"
	};
   	var col = ClearBlade.Collection({collectionName:"<COLLECTION_NAME>"});
    col.addColumn(addColumnOptions, callback);
~~~

## Collection.count(_query, callback)

Retrieves the count of items in a collection that match the supplied query

* @param {Query} _query - Query object that used to define what item or set of items to remove. Optional
* @param {function} callback - Function that handles the response from the server

**Basic Example:**

~~~javascript
   var callback = function (err, data) {
        if (err) {
        	resp.error("count error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var col = ClearBlade.Collection({collectionName: "<COLLECTION_NAME>"});
    col.count(callback);
    //this returns an object in the form of {count: 5}

    Example with Query:

    var callback = function (err, data) {
        if (err) {
        	resp.error("count error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var col = ClearBlade.Collection({collectionName: "<COLLECTION_NAME>"});
   	var query = ClearBlade.Query();
   	query.equalTo("name", "John");
    col.count(query, callback);
    //this returns an object in the form of {count: 2}

~~~

# Database

Class: ClearBlade.Database();

This class allows you to specify the query in raw SQL on platform and edge instead of using the existing ClearBlade query model. The function can be used for operations, such as `JOIN` and `SOME`, that are not supported by the ClearBlade Query Model.

Use the Database object without the options object for performing query and exec operations on collections.

~~~javascript
		var db = ClearBlade.Database();
~~~

Use the Database object with the external database options object for performing operations on external database connections.

~~~javascript
		 var db = ClearBlade.Database({externalDBName: "externalDB"});
~~~

## ClearBlade.Database.query(_query, callback)

This function returns query results to be parsed.

~~~~javascript
	var db = ClearBlade.Database();
	var callback = function (err, data) {
        if (err) {
        	resp.error("Parse error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
	db.query("select sum(ingress) from traffic where entrance='Main_Entrance';", callback);
	//'ingress' is a column in 'traffic' collection
    };
~~~~

## ClearBlade.Database.exec(_query, callback)

This function does not return query results. 

* @param {Query} _query - Query object that used to define what operations are being used. 
* @param {function} callback - Function that handles the response from the server

~~~~javascript
	var db = ClearBlade.Database();
	var callback = function (err, data) {
        if (err) {
        	resp.error("Parse error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
	db.exec("delete from traffic where entrance='Store_Entrance';");
~~~~

## ClearBlade.Database.performOperation(callback, argument)

This function takes a callback as the first argument and a variable number of string arguments after the callback.

* @param {function} callback - Function that handles the response from the server
* @param {...string} arguments - Arguments that are used in the external databases.

We define how the param `arguments` looks like for each database below.

### MongoDB 

dbCommand: valid mongodb commands 

Example command forms:

`db.collectionName.insert()` 

or 

`db.<collectionName>.<collectionMethod>(<data>).<cursorMethod>(<data>)`


__Collection methods that are supported__: 
* find
* insert
* insertOne
* insertMany
* update
* updateOne
* updateMany
* deleteOne
* deleteMany
* countDocuments
* estimatedDocumentCount
* aggregate

__The following Cursor methods are supported:__
* sort
* limit
* skip
* collation

MongoDB CLI commands can be found here -  https://docs.mongodb.com/manual/reference/method/js-collection/
  
~~~~javascript

    var db = ClearBlade.Database({externalDBName: "externalDB"});
	var dbCommand = 'db.collectionName.find()'
	var callback = function(err, data) {
  		if(err) {
   		 	resp.error("Error performing external db operation: " + JSON.stringify(data))
  		} else {
    		resp.success(data);
  		}
	db.performOperation(callback, dbCommand)
~~~~

### SQL 
All SQL queries are supported

- arguments:
  - query
  - (optional) args: which are denoted by `$1`, `$2`, ... in the query string

~~~~javascript
	var db = ClearBlade.Database({externalDBName: "externalDB"});
	var sqlQuery1 = "SELECT * from myTable where name='Bob'"
	var callback = function(err, data) {
  		if(err) {
   		 	resp.error("Error performing external db operation: " + JSON.stringify(data))
  		} else {
    		resp.success(data);
  		}
	db.performOperation(callback, sqlQuery1)
~~~~


~~~~javascript
// Another way to invoke the db.performOperation, this example is in continuation of the above example, just changing values for few of the variables.
var sqlQuery2 = "SELECT * from myTable where name=$1"
	db.performOperation(callback, sqlQuery2, "Bob") // 3rd arg will be substitution for $1
~~~~

### CouchDB

- arguments
  - httpMethod: example "POST"
  - uri: example "/mydb/bulk_docs"
  - data: example: "{\"docs\": [{\"name\": \"Bob\", \"age\": 100}]}"
  `data` is optional and is unavailable for `GET` operations.

Please use the APIs listed here - https://docs.couchdb.org/en/stable/api/index.html

~~~~javascript
	var db = ClearBlade.Database({externalDBName: "externalDB"});
	var httpMethod = "GET"
	var uri = "/myDb/all_docs"
	var callback = function(err, data) {
  		if(err) {
   		 	resp.error("Error performing external db operation: " + JSON.stringify(data))
  		} else {
    		resp.success(data);
  		}
	db.performOperation(callback, httpMethod, uri)
~~~~

~~~~javascript
// Another way to invoke the db.performOperation, this example is in continuation of the above example, just changing values for few of the variables.
	var httpMethod = "POST"
	var uri = "/myDb/bulk_docs"
	var data = {
		"docs": [{"name": "Bob", "age": 100}]
	}
	db.performOperation(callback, httpMethod, uri, JSON.stringify(data))
~~~~

## ClearBlade.Database.performOperationAsync(callback, argument)

This function is an asynchronous version of ```performOperation```. This function takes a callback as the first argument and a variable number of string arguments after the callback. This function does not return the results of the operation. It only returns ___"Success"___ if the operation succeeded or an error message if the operation failed.

* @param {function} callback - Function that handles the response from the server
* @param {...string} arguments - Arguments that are used in the external databases.  


# Class: ClearBlade.Device(options)

To instantiate a Device object all you need to do is call:

~~~javascript

	var dev = ClearBlade.Device();
~~~

The available methods for the Device class and examples of how to use them are listed below:

## Device.fetch(query, callback)

Reqests a device or a set of devices from the devices collection.

* @param {Query} _query Used to request a specific device or subset of devices from the devices collection on the server. Optional. If a query is not specified, __ALL__ devices will be returned.
* @param {function} callback Supplies processing for what to do with the data that is returned from the collection

Basic Example:

~~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("fetch error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

   	var dev = ClearBlade.Device();
    dev.fetch(callback);
~~~~
Example with Query:
~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("fetch error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    var dev = ClearBlade.Device();

    var query = ClearBlade.Query();
    query.columns(["name", "type", "state"]);
    query.equalTo("type", "Weight Sensor");

    dev.fetch(query, callback);
~~~

## Device.create(device, callback)

Creates a new device in the devices collection.

* @param {object} device A json object representing the device to be created. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns, and will be used to initialize the contents of the respective column.
* @param {function} callback Supplies processing for what to do with any returned data when the invocation is completed

Example:

~~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("create error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    var dev = ClearBlade.Device();
    var newDevice = {
        "name": "MyNewDevice",
        "allow_key_auth": false,
        "allow_certificate_auth": true,
        "enabled": false,
        "state": "",
        "type": "",
        "active_key": ""
    }

    dev.create(newDevice, callback);
~~~~

## Device.update(query, changes, callback)

Updates a device or a set of devices from the devices collection.

* @param {Query} query Used to request a specific device or subset of devices from the devices collection on the server. Optional.
* @param {object} changes A json object representing the device columns to be updated. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns, and will be used to update the contents of the respective column.
* @param {function} callback Supplies processing for what to do with any returned data when the invocation has been completed

Example:

~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("fetch error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    var dev = ClearBlade.Device();

    var query = ClearBlade.Query();
    query.columns(["name", "type", "state"]);
    query.equalTo("type", "Weight Sensor");

    dev.fetch(query, callback);
~~~

## Device.delete(query, callback)

Deletes a device or a set of devices from the devices collection.

* @param {Query} _query Used to delete a specific device or subset of devices from the devices collection on the server. REQUIRED. If a query is not specified, an error will be returned, preventing the inadvertent situation where all devices are deleted.
* @param {function} callback Supplies processing for what to do with the any returned data when the invocation is completed

Example:

~~~javascript
    var callback = function (err, data) {
        if (err) {
        	resp.error("delete error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    var dev = ClearBlade.Device();

    var thequery = ClearBlade.Query();
    thequery.equalTo("name", "Device 2");

    dev.delete(thequery, callback);
~~~

## Messaging

Class: ClearBlade.Messaging()

To instantiate a Messaging object all you need to do is call:

~~~javascript

	var msg = ClearBlade.Messaging();
~~~

The available methods for the Messaging class and examples of how to use them are listed below:

## Messaging.getMessageHistory(topic, start, count, callback)

Retrieves the message history for a topic within the specified parameters.

* @param {string} topic - String that signifies which topic to search
* @param {number} start - Epoch timestamp in seconds that will retrieve 'count' number of messages after that timestamp
* @param {number} count - Number that signifies how many messages to return; 0 returns all messages
* @param {function} callback - Function that handles the response from the server

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
    var unixTimeMilli = new Date().getTime()
    // Get messages from last 60 seconds
    var timestampMinAgo = unixTimeMilli - 60;
	msg.getMessageHistory("coolTopic", timestampMinAgo, 25, function(err, data) {
		if(err) {
			resp.error("message history error : " + JSON.stringify(data));
		} else {
			resp.success(data);
		}
	});
~~~

## Messaging.subscribe(topic, callback)

Subscribes to a MQTT message topic.

Prereq: The user's role should have permissions to subscribe to that topic. Verify on the `Roles` Page of `ClearBlade Console`.

Callback subscribe:

* @callback subscribeCallback
* @param {boolean} err - Is true if there is an error.
* @param {Object} data - the response from the MQTT broker.


Function Subscribe:

* @param {string} topic - String that signifies which topic to subscribe
* @param {function} subscribeCallback - Function that handles the response from the mqtt broker

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();

	msg.subscribe("coolTopic", function(err, data) {
		if(err) {
			resp.error("subscribe error : " + JSON.stringify(data));
		} else {
			resp.success(data);
		}
	});
~~~

## Messaging.waitForMessage(topics, callback)

This method waits for message on the array of topics it is provided. Usually used within Stream Services.

Callback waitForMessage:

 * @callback waitForMessageCallback
 * @param {boolean} err - Is true if there is an error
 * @param {string} msg - The message which gets published on the topic
 * @param {string} topic - It's one of the topics which waitForMessage was listening on


Function waitForMessage:

* @param {[]string} topics - An array of topics to wait for messages on
* @param {function} waitForMessageCallback - Function that handles the response from the server

**Example:**

~~~javascript
	const msg = ClearBlade.Messaging();
    const topics = ["topic1", "topic2"];
	msg.subscribe("topic1", function(err, data){

	})

	msg.waitForMessage(topics, function(err, msg, topic) {
		if(err) {
			resp.error("message history error : " + JSON.stringify(msg));
		}

		if( topic === "topic1" ){
			// perform some action
		} else {
			//topic2
			// perform some action
		}

	});
~~~

## Messaging.getMessageHistoryWithTimeFrame(topic, count, last, start, stop, callback)

Retrieves the message history for a topic within the specified parameters.

* @param {string} topic - String that signifies which topic to search
* @param {number} count - Number that signifies how many messages to return; 0 returns all messages
* @param {int} last - Epoch timestamp in seconds that will retrieve 'count' number of messages before that timestamp
* @param {int} start - Epoch timestamp in seconds that will retrieve 'count' number of  messages within timeframe
* @param {int} stop - Epoch timestamp in seconds that will retrieve 'count' number of  messages within timeframe
* @param {function} callback - Function that handles the response from the server

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
	var unixTimeMilli = new Date().getTime();
	msg.getMessageHistoryWithTimeFrame("coolTopic", 25, unixTimeMilli, null, null, function(err, body) {
		if(err) {
			resp.error("message history error : " + JSON.stringify(data));
		} else {
			resp.success(body);
		}
	});
~~~

## Message.publish(topic, payload)

Publishes to a topic.

* @param {string} topic - Is the topic path of the message to be published. This will be sent to all listeners on the topic. No default.
* @param {string} payload - The payload to be sent. Also no default.

{{< warning title="Topic Restrictions" >}}
A topic is comprised one or more topic levels (ex. 'factory/1/device/3' is a single topic with four topic levels) The '/' is a reserved character to separate topic levels, and thus, a topic level cannot contain '/' in its name. However, spaces and punctuation are allowed.
{{< /note >}}
**Example:**

~~~javascript
    var msg = ClearBlade.Messaging();
    msg.publish("ClearBlade/is awesome! Topic","ClearBlade totally rules");
~~~

## Message.getCurrentTopics(callback)

Returns the current topics for a system

* @param {function} callback - Function that handles the response from the server

**Example:**
~~~javascript

	var callback = function (err, data) {
		if(err) {
			resp.error("Unable to retrieve current topics: " + JSON.stringify(data));
		} else {
			resp.success(data);
		}
    };

    var msg = ClearBlade.Messaging();
    msg.getCurrentTopics(callback);
~~~

## Message.getAndDeleteMessageHistory(topic, count, last, start, stop, callback)

 This call deletes all of the message history for mqtt topic 'topic'. It has 4 options that you can set when you delete the message history. 'count' is the number of messages you wish to delete. For count = 0, all messages for that topic are deleted. You can also use either the 'last' or the 'start and stop' options to widen you search. The 'last' options which is an epoch timestamp in seconds will retrieve and delete 'count' number of messages before that timestamp. The 'start and stop' options which are also an epoch timestamp will retrieve and delete 'count' number of messages within that timeframe. For each of these cases you can either set 'count' to zero or a non zero value depending on how many messages you wish to delete.

 The return value is a list of js objects, one for each returned item in the message history.

 * @param {string} topic - String that signifies which topic to search
 * @param {number} count - Number that signifies how many messages to return and delete; 0 returns and deletes all messages
 * @param {int} last - Epoch timestamp in seconds that will retrieve and delete 'count' number of messages before that timestamp
 * @param {int} start - Epoch timestamp in seconds that will retrieve and delete 'count' number of  messages within timeframe
 * @param {int} stop - Epoch timestamp in seconds that will retrieve and delete 'count' number of  messages within timeframe
 * @param {function} callback - Function that handles the response from the server

**Example:**
~~~javascript

	var callback = function (err, data) {
		if(err) {
			resp.error("getcurrenttopics error: " + JSON.stringify(data));
		} else {
			resp.success(data);
		}
    };

    var msg = ClearBlade.Messaging();
    msg.getAndDeleteMessageHistory("TestTopic", 0, null, null, null, callback); // get and delete all messages for "TestTopic"
~~~

## messaging.setTimeout(timeout, topic, data, callback)

This method publishes data to a topic after timing out.

@callback cbSetTimeout
 * @param {boolean} err - Is true if there is an error
 * @param {string} msg - timerId if no error, error message otherwise

function setTimeout:
 * @param {number} timeout - Timeout in miliseconds
 * @param {string} topic - String that signifies which topic to search
 * @param {string} data - Data to publish to the topic once it timeouts
 * @param {cbSetTimeout} callback - Function that handles the response from the server



**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
	var callback = function(err, msg) {
		if(err) {
			resp.error("Timeout error : " + JSON.stringify(msg));
		}
		else {
			//msg will be the timerId
			resp.success(msg)
		}
	}
	msg.setTimeout(30, "TestTopic", "4f775222-1910-4bd7-bf8d-5bdbc11dfa92", callback );
~~~

## messaging.cancelCBTimeout(timerId, callback)

This method cancels the timeout message with the given timerId.

@callback cancelCBTimeout:

 * @param {function} callback - Function that handles the response from the server
 * @param {boolean} err - Is true if there is an error
 * @param {string} timerId -  if there is no error, error message otherwise

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
	var callback = function(err, msg) {
		if(err) {
			resp.error(" Cancel Timeout error : " + JSON.stringify(msg));
		}
		else {
			resp.success(msg)
		}
	}
	msg.cancelCBTimeout("90cc94d10bb0d9acfdaa8cb684fd01", callback);
~~~


## messaging.setInterval(timeout, topic, data, iterations, callback)

This method publishes data to topic after timing out, repeats iterations.

@callback cbSetInterval:
 * @param {boolean} err - Is true if there is an error
 * @param {string} msg - timerId if no error, error message otherwise


function setInterval:

 * @param {number} timeout - Timeout in milliseconds
 * @param {string} topic - The topic to publish to, after every timeout
 * @param {string} data - Data to publish to the topic once it timeouts
 * @param {number} iterations - Number of times timeout is set
 * @param {function} callback - Function that handles the response from the server

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
	var callback = function(err, msg) {
		if(err) {
			resp.error("Interval error : " + JSON.stringify(msg));
		}
		else {
			//msg will be the intervalId
			resp.success(msg)
		}
	}
	msg.setInterval(30, "TestTopic", "8e48cb42-d76b-4b82-8253-1e8af8e20795", 1, callback );
~~~

## messaging.cancelCBInterval(intervalId, callback)

This method cancels the interval message with the given intervalId.
.

@callback cancelCBInterval:

 * @param {function} callback - Function that handles the response from the server
 * @param {boolean} err - Is true if there is an error
 * @param {string} intervalId -  if there is no error, error message otherwise

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
	var callback = function(err, msg) {
		if(err) {
			resp.error("Cancel Interval error : " + JSON.stringify(msg));
		}
		else {
			resp.success(msg)
		}
	}
	msg.cancelCBInterval("90cc94d10bb0d9a0fdca8cb684fd01", callback);
~~~

# Class: ClearBlade.Code()

The code class is used to call other code services from within a service.
To instantiate the code class just call:

~~~javascript
	var code = ClearBlade.Code();
~~~

## Code.execute(name, params, loggingEnabled, callback)

Executes another code service.

~~~javascript
/**
* @param {string} name - String representing the name of the code service that should be executed.
* @param {Object} params - Object that contains all the parameters that will be used for the code service. Optional.
* @param {Boolean} loggingEnabled - Boolean that represents whether or not the code service should use logging
* @param {callback} To handle response from code service
*/

function MyService(req, resp){
	ClearBlade.init({request:req})
	var codeEngine = ClearBlade.Code()
	var serviceToCall = "RegisterWizards"
	var loggingEnabled = true
	var params = {
		first_name:"Harry",
		last_name:"Potter"
	}		
	codeEngine.execute(serviceToCall, params, loggingEnabled, function(err, data){
		if(err){
			resp.error("Failed to complete my service: " + JSON.stringify(data))
		}
		resp.success("Yay! Here is my response: " + JSON.stringify(data))
	})
}
~~~

# Class: ClearBlade User()

~~~javascript
	var user = ClearBlade.User();
	user.getUser(function(err, data) {
		resp.success(JSON.stringify(data));
	});
~~~

This class allows for interacting with user information.

## User.getUser(callback)
Returns the user table row for the user that the service is running as.


## User.allUsers(query,callback)
Accepts a ClearBlade.Query object and a callback. It will return all users that match on the query. (excepting attempting to match on the password field)

## User.setUsers(query,changes,callback)  
operates on the users table in much the same way update works on a collection.

```javascript

    ClearBlade.init({request:req})
	var user = ClearBlade.User();
    var changes = {
            edge_access: JSON.stringify(["edge1","edge3"])
        };
    log({changes})
    // Set the email to modify, replace "provisioner@clearblade.com"
    var q = ClearBlade.Query().equalTo("email","provisioner@clearblade.com");
    user.setUsers(q,changes, function(err,bod){
      if(err){
        resp.error("error changing user: " + JSON.stringify(bod));
      }else{
        log("success body:" + JSON.stringify(bod));
        resp.success(JSON.stringify(bod));
      }
    });

```

## User.setUser(changes,callback)
updates just the user the service is running as.

## User.count(query,callback)  
returns a count of users who have matched the query


## Timer

Events: ClearBlade Timer


## ClearBlade.Timer.Create(name, options, callback)
### Create a new Timer (runs a function at a set interval)
```javascript
ClearBlade.Timer.Create("runValidatorEveryHour",
    {
        frequency: 3600,
        service_name: "validator"
		repeats: 1,
        description:"example"
    },
    function(err, response) {
        if (err) {
            resp.error(response)
        }
        resp.success("Timer successfully created");
    });
```


## ClearBlade.Timer.Fetch(name, callback)
### Fetch an existing Timer


## timer.Update(options, callback)
```javascript
ClearBlade.Timer.Fetch("runValidatorEveryHour", function (err, timer) {
        if (err) {
            resp.error(response);
        } else {
            timer.Update({def_module:"<COLLECTION_ID>",def_name:"bar"}, function (err, response){
                if (err) {
                    resp.error(response);
                }
                resp.success("Timer successfully updated");
            });
        }
    });
```


## timer.Delete(name, callback)
```javascript
ClearBlade.Timer.Fetch("runValidatorEveryHour", function (err, timer) {
        if (err) {
            resp.error(timer);
        }
        timer.Delete(function (err, response) {
            if (err) {
                resp.error(response);
            }
            resp.success("Timer deleted");
            });
    });
```



## Trigger

Events: ClearBlade Trigger


## ClearBlade.Trigger.Create(name, options, callback)
### Create a new Trigger (runs a service after a defined action is detected)
```javascript
ClearBlade.Trigger.Create("runTestAfterPublishOnTopicFoo", {
        "def_module":      "Messaging",
        "def_name":        "Publish",
        "key_value_pairs": {topic:"foo"},
        "service_name":    "test",
    },
    function cb(err, t) {
        resp.success("OK");
    });
```


## ClearBlade.Trigger.Fetch(name, callback)
### Fetch an existing Trigger


## trigger.Update(options, callback)
```javascript
ClearBlade.Trigger.Fetch("runTestAfterPublishOnTopicFoo", function (err, trigger) {
        if (err) {
            resp.error(trigger);
        }
        trigger.Update({
            def_module:"COLLECTION_ID",
            def_name:"bar"
        }, function(err, response){
            if (err) {
                resp.error(response);
            }
            resp.success("Trigger updated");
        });
    });
```


## trigger.Delete(options, callback)
```javascript
ClearBlade.Trigger.Fetch("unTestAfterPublishOnTopicFoo", function(err, trigger) {
        if (err) {
            resp.error(trigger);
        }
        trigger.Delete(function(err, response) {
            if (err) {
                resp.error(response);
            }
            resp.success("Trigger deleted");
        });
    });
```

## Edge

### ClearBlade.isEdge()

Returns true if code service is executing on an Edge. Otherwise, false

~~~javascript
function myService(req, resp){
	ClearBlade.init({request:req})
	if( ClearBlade.isEdge() ){
		resp.success("I am an Edge!")
	}
	resp.error("This is not an Edge, this is running on a platform")
}
~~~

### ClearBlade.edgeId()

Returns the name of Edge if running on an Edge, else returns empty string

~~~javascript
function myService(req, resp){
	ClearBlade.init({request:req})
	var edgeId = ClearBlade.edgeId()
	resp.success("Edge Id: " + edgeId)
}
~~~
