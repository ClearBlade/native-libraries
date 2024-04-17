The ClearBlade library provides all the methods necessary for interacting with the ClearBlade Platform and its resources from within a code service.

# Usage

1. __[Initialization](#init)__
2. __[Device](#device)__
3. __[Cache](#cache)__
4. __[Query](#query)__
5. __[Collection](#collection)__
6. __[Database](#database)__
7. __[Messaging](#messaging)__
8. __[Timers](#timer)__
9. __[Triggers](#trigger)__
10. __[Edge](#edge)__
11. __[Roles](#roles)__
12. __[Permissions](#permissions)__
13. __[Lock](#lock)__
14. __[Deployments](#deployments)__
15. __[Code](#code)__

# Overview

ClearBlade.js library enables users to:

>1. Query a local or remote data collection
>2. Listen to an MQTT topic and process the payload
>3. Store data in a collection
>3. Modify devices attributes
>4. Create a timer or trigger
>5. Register new users
>6. Log users in or out
>1. Delete users


# ClearBlade

The available methods for the ClearBlade class and examples of how to use them are listed below.


## init

This method sets up the ClearBlade object with a few configuration values necessary for subsequent operations against the ClearBlade Platform.

Note: You must init before accessing any ClearBlade resources within your system

Passing the request into the init function is the simplest and most performant way to establish an authorized instance of the ClearBlade object. The ClearBlade object will inherit the permissions associated with the calling user.

Recommended:

~~~javascript
	//In this example, the passed-in information on the request object is used to init the ClearBlade object.
	ClearBlade.init({request:req});
~~~

To establish a new session for the ClearBlade object, the `options` object contains all the parameters passed into the `init` method. It can contain the following keys/value pairs:

Mandatory parameters:

>- systemKey: The system key of the system you would like to access (available in the req object).

>- systemSecret: The system secret of the system you would like to access (available in the req object).


Optional parameters:

>- email: The email of the user you would like to log in/register for subsequent operations.
>- password: The password of the user you would like to log in/register for subsequent operations.
>- registerUser: Boolean that signifies that the email and password params should be used to register a new user with the Platform. The newly registered user will be logged in.
>- useUser: An object in the form of {"authToken": "userToken"} used to set the user for any ensuing operations.
>- callback: A function that takes a boolean for error checking and a JSON object. Must be supplied if email and password options are provided.


Basic example:
~~~javascript
		//This example initializes the ClearBlade object with the desired system.
		ClearBlade.init({request:req});
~~~

Authentication example:

~~~javascript
		//This example uses a users email and password to log in.
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

Note: Invalid credentials (bad username or password) leads to the #init methods throwing an error. You must catch this error using a try catch block.

Registration example:
~~~javascript
		//This example uses an email and password to register a new user with the Platform.
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
		// Uses an email and auth token of a pre-authorized user: no need for a callback.
		ClearBlade.init({
			systemKey: req.systemKey,
			systemSecret: req.systemSecret,
			useUser: {
				email: req.userid,
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

This method will log into the ClearBlade Platform anonymously and use the token for subsequent requests.

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

This method checks whether the current users authentication token is still valid.

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

This method logs a user into the ClearBlade Platform before making requests.

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

This method is used to log the user out of the Platform.

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
ClearBlade.init({ request: req });
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
ClearBlade.init({ request: req });
emailsToDelete = ["<USER_EMAIL_ONE>", "<USER_EMAIL_TWO>", "<USER_EMAIL_THREE>"];
ClearBlade.deleteUsers(emailsToDelete, function(err, body) {
	if(err) {
		resp.error("user deletion failure: " + JSON.stringify(body));
	} else {
		resp.success(body);
	}
});
```

## ClearBlade.reauthUser(usertoken, refreshtoken, callback)

This method is used to reauthenticate the user with refresh tokens. 

Example:

```javascript
ClearBlade.init(req);
var usertoken = "<old_auth_token>";
var refreshtoken = "<old_refresh_token>";
ClearBlade.reauthUser(usertoken, refreshtoken, function(err, data) {
	if(err) {
		resp.error("user reauthentication failure: " + JSON.stringify(data));
	} else {
		resp.success(data);
	}
});
```

## Device

ClearBlade.getAllDevicesForSystem(callback)

This method retrieves all devices from the Devices Auth table. If successful, the response will contain an array of every device defined within a system. Each array element will be a JSON object whose attributes correspond to the columns in the Devices table, including any custom columns.

~~~json
[
  {
    "__HostId__": "20b6b826-39f3-4a23-9559-62099d08c42a",
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

This method retrieves a device from the Devices Auth table. If successful, the response will contain a JSON object representing the requested device. The attributes in the JSON object correspond to the columns in the Devices table, including any custom columns.

~~~json
{
  "__HostId__": "20b6b826-39f3-4a23-9559-62099d08c42a",
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

An error will be returned in the __data__ parameter passed to the callback function if unsuccessful.
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

This method creates a new device in the Devices Auth table. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns. They will be used to initialize the contents of the respective column. If successful, the __data__ parameter passed to the callback will contain a JSON object representing the created device.

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

An error will be returned in the __data__ parameter passed to the callback function if unsuccessful.
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

	// Default is true so that device table changes can trigger code services.
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

This method is used to update a device in the Devices Auth table. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns. They will be used to initialize the contents of the respective column. If successful, the __data__ parameter passed to the callback will contain a __success__ attribute.

{{< warning title="Heads Up!" >}}
Once a device has been created, you cannot change its name. Do not specify the __name__ attribute when updating a device.
{{< /warning >}}

~~~json
{
  "success": true
}
~~~  

An error will be returned in the __data__ parameter passed to the callback function if unsuccessful.
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

    // Default is true so that device table changes can trigger code services.
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

This method creates a device in the Devices Auth table. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns. They will be used to initialize the contents of the respective column. If successful, the __data__ parameter passed to the callback will contain a __success__ attribute.

~~~json
{
  "success": true
}
~~~  

An error will be returned in the __data__ parameter passed to the callback function if unsuccessful.
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

	// Default is true so that device table changes can trigger code services.
	var DEVICE_TRIGGER_ENABLED = true

	ClearBlade.deleteDevice("<DEVICE_NAME>", DEVICE_TRIGGER_ENABLED, function(err, data) {
		if(err) {
			resp.error("Unable to update device: " + JSON.stringify(data))
		}

		resp.success(data);
	});
~~~

## ClearBlade.connectedDevices(callback)

This method retrieves information about the devices connected to the platform via MQTT.
The response is a map from the device names to a list of connection details.
The connection details include the clientID the device connected with and the time it started the connection (UTC).

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

This method retrieves information about a single device connected to the platform via MQTT.
The response is a map containing all the usual device details and a list of connection details for each active connection.
The connection details include the clientID the device connected with and the time it started the connection (UTC).

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

This method retrieves the number of devices connected to the platform via MQTT.
The response contains unique\_device\_connections, total\_device\_connections, and total\_devices keys.
Total devices is a device count in your device table.
Unique device connections are how many of those devices have at least one active MQTT connection to the Platform.
Total device connections are how many active MQTT connections to the Platform are made by your devices.

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

Caches can be shared across services and make data access faster than database operations. The ClearBlade.Cache object performs cache operations like GET, GETALL, SET, SETNX, SETMULTIPLE, DELETE, and FLUSH. __Please note that setting `undefined` values in the cache causes abnormal behavior.__  

To instantiate the cache object, you need your cache name.

~~~javascript
	var cache = ClearBlade.Cache('<CACHE_NAME>');
~~~

### Cache Callback

The following callback function can be used for all cache callbacks.

@callback cbCallback:  
@param {boolean} err: Is true if an error exists  
@param {string} data: Data or error description in case of an error

## Cache.set(key, value, callback)
Sets data in the cache. Requires that the Cache object was initialized with a cache name. On success, this returns a "Set done" string.

* @param {string} key: Key in cache
* @param {string|number|object|array|boolean|null} value: Data to be stored in the cache
* @param {function} callback: Returns error or success messages

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
Sets data in the cache if it does not exist. Requires that the cache object was initialized with a cache name. On success, this returns a boolean string. It returns boolean __true__ if it sets the cache data and boolean __false__ if the data already exists.

* @param {string} key: Key in cache
* @param {string|number|object|array|boolean|null} value: Data to be stored in the cache
* @param {function} callback: Returns error or success messages

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
Sets multiple entries in the cache at once. Requires that the cache object was initialized with a cache name. Requires that the data passed to this function is a JSON object. On success, returns a "SetMultiple done" string.

* @param {object} data: Multiple data entries to be stored in the cache
* @param {function} callback: Returns error or success messages

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
Gets data corresponding to the key from the cache. Requires that the cache object was initialized with a cache name. There are two possible return values on success:       
	- If key not found, returns undefined.   
	- If key found, returns data. __(Type of data is same as set in the cache)__.   

* @param {string} key: Key in cache
* @param {function} callback: Returns an error or data corresponding to the key

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
Gets all data from the cache. Requires that the cache object was initialized with a cache name. On success, returns all data as a JSON object with key/value pairs. If the cache is empty, an empty JSON object is returned.

* @param {function} callback: Returns an error or all data from the cache

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
Deletes data corresponding to the cache's key. Requires that the cache object was initialized with a cache name. On success, returns a "Delete done" string.

* @param {string} key: Key in cache
* @param {function} callback: Returns error or success messages

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
Deletes all data from the cache. Requires that the cache object was initialized with a cache name. On success, returns a "Flush done" string.

* @param {function} callback: Returns error or success messages

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

Queries allow retrieving and updating data in collections based on criteria. The ClearBlade.Query object is used to create the query rapidly and perform possible fetch, update, create, and delete actions.

{{< note title="Query Default Page Size" >}}
The query will return 100 results by default unless specified by Query.setPage.
{{< /note >}}

To instantiate a query object, you have three options:

>- collectionName (string): The collection name you want to access
>- collectionID (string): The collection ID you want to access
>- collection (string): The collection ID you want to access

1) Supply an object containing the <COLLECTION_NAME>
~~~javascript
		var query = ClearBlade.Query({collectionName:"<COLLECTION_NAME>"});
~~~
Alternatively, the collectionID key can be used with the <COLLECTION_ID> value.
~~~javascript
		var query = ClearBlade.Query({collectionID:"<COLLECTION_ID>"});
~~~
Additionally, the <COLLECTION_ID> can be used.
~~~javascript
		var query = ClearBlade.Query("<COLLECTION_ID>");
~~~

2) Supply nothing (this only applies when using the methods on the ClearBlade.Collection or ClearBlade.User classes to make requests against data).
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
	// If applicable, apply a second sort for entries with the same value for column colName.
	query.ascending("<COLUMN_NAME_2>");
~~~~

## Query.descending(field)
This method is used to sort the response from the query by a particular field in descending order.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.descending("<COLUMN_NAME_1>");
	// If applicable, apply a second sort for entries with the same value for column colName.
	query.descending("<COLUMN_NAME_2>");
~~~~

{{< warning title="Please Note: Timestamps" >}}
ClearBlade timestamp types are in ms (milliseconds), not ns (nanoseconds). If you use the JavaScript Date Object, you will need to /= 1000.
{{< /warning >}}

## Query.equalTo(field, value)
This method filters the data using an equality operator on a given column.
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
This method filters the data using a greater than operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.greaterThan("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.greaterThanEqualTo(field, value)
This method filters the data using a greater than equal to operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.greaterThanEqualTo("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.lessThan(field, value)
This method filters the data using a less than operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.lessThan("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.lessThanEqualTo(field, value)
This method filters the data using a less than equal to operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.lessThanEqualTo("YOUR_COLUMN_OF_TYPE_INT", 4);
~~~~

## Query.notEqualTo(field, value)
This method filters the data using a not equal to operator on a given column.
~~~~javascript
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	query.notEqualTo("YOUR_COLUMN_OF_TYPE_STRING", "hello");
~~~~

## Query.rawQuery(rawQueryString)
This method is used to supply a raw query string to the query object.  
__NOTE:__ This is currently only supported for MongoDB. This will override all other query operations like 
equalTo, lessThan, greaterThan, etc. You can still use ___Query.ascending___,
___Query.descending___ or ___Query.setPage___ to set sort order or paging.   
~~~~javascript
	// MongoDB query example
	var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
	var rawQueryJson = {"age": {"$exists": true, "$lt": 45}}
	query.rawQuery(JSON.stringify(rawQueryJson));
~~~~

## Query.setPage(pageSize, pageNum)

The default paging is page 1 with 100 results.  

This method sets the desired page and page size for the request.
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
Chains an existing query object to the query object with or on the same column: title
~~~~javascript
   // Assume your collection's name is Activity
	var query1 = ClearBlade.Query({collectionName: "Activity"})
	var query2 = ClearBlade.Query({collectionName: "Activity"})

	query1.equalTo('title','Engineer')
	query2.equalTo('title','Manager')

	// If an entry has Engineer or Manager in the title column
	// they will be fetched with finalQuery.
	var finalQuery = query1.or(query2)
~~~~

Chains an existing query object to the query object with or on different columns: title and city

~~~~javascript
	// Assume your collection's name is Activity
	var query1 = ClearBlade.Query({collectionName: "Activity"})
	var query2 = ClearBlade.Query({collectionName: "Activity"})

	query1.equalTo('title','Engineer')
	query2.equalTo('city','Austin')
	// If an entry has Engineer in the title column
	// or Austin in the city column
	// they will be fetched with finalQuery
	var finalQuery = query1.or(query2)
~~~~

## Query.fetch(callback)
Requests an item or a set of items from the query. Requires that the query object was initialized with a collection.

~~~~javascript
	//@param {function} callback: Supplies processing for what to do with the data that is returned from the collection.
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

Updates an existing item or set of items. Requires that a collection was set when the query was initialized.


**Example:**
~~~~javascript
	// @param {Object} changes: Object representing the attributes that you want to be changed
	// @param {function} callback: Function that handles the server's response
    //This example assumes a collection with name and age columns.
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
Removes an existing item or set of items. Requires that a collection was set when the query was initialized.
**Example:**
~~~~javascript
	//@param {function} callback Function that handles the server's response
    var query = ClearBlade.Query({collectionName: "<COLLECTION_NAME>"});
    //This example contains the item whose name value is John.
    query.equalTo('name', 'John');
    var callback = function (err, data) {
        if (err) {
        	resp.error("removal error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
    };

    query.remove(callback);
    //Removes every item whose name attribute equals John.
~~~~

## Collection

Class: ClearBlade.Collection(options)

To instantiate a collection object, you can supply an object containing <COLLECTION_NAME>, <COLLECTION_ID>, or a string for the collectionID. Here are the object's mutually exclusive values:

* **collectionName** (string): The collection name you want to access
* **collectionID** (string): The collection ID you want to access
* **collection** (string): The collection ID you want to access

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

### jsonb

Collections now support column type `jsonb`. This enables users to store JSON values in the database tables and query based on keys (for objects) or indexes (for arrays). 

_Note_: Postgres and SQLite have different styles to operate on JSON data. Postgres has JSON operators and functions whereas SQLite only has JSON functions. You must write different raw queries for Postgres and SQLite and use the `ClearBlade.IsEdge()` function call to check if your code is running on an edge or the Platform.
The [ClearBlade Query](#query) object only supports one JSON operator `->>`. This works across Postgres and SQLite. To use all other operators/functions, a raw query is required.

Click __[HERE](https://www.sqlite.org/json1.html)__ to see a list of SQLite JSON functions.

Click __[HERE](https://www.postgresql.org/docs/current/functions-json.html)__ to see a list of Postgres JSON operators/functions.

The available methods for the collection class and examples of how to use them are listed below.

## Collection.fetch(query, callback)

Requests an item or a set of items from the collection.

* @param {Query} _query Used to request a specific item or subset of items from the collection on the server. Optional.
* @param {function} callback Supplies processing for what to do with the data that is returned from the collection.

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

* @param {Object} newItem: An object that represents an item that you want to add to the collection
* @param {function} callback: Supplies processing for what to do with the data that is returned from the collection

**Example:**
~~~javascript
   	//This example assumes a collection with name, height, and age columns.
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
    //This inserts the newPerson item into the collection that col represents
~~~

## Collection.update(_query, changes, callback)

Updates an existing item or set of items

   * @param {Query} _query Query object to denote which items or set of items will be changed
   * @param {Object} changes Object representing the attributes that you want to be changed
   * @param {function} callback Function that handles the server's response


**Example:**

~~~javascript
    //This example assumes a collection with name and age columns.
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

   	var col = ClearBlade.Collection({collectionName:"<COLLECTION_NAME>"});
    col.update(query, changes, callback);
    //sets John's age to 23

~~~

## Collection.remove(_query, callback)

Removes an item or set of items from the specified collection

* @param {Query} _query Query object that defines what item or set of items to remove
* @param {function} callback Function that handles the server's response

**Example:**
~~~javascript
    //This example assumes that you have a collection with the item whose name attribute is John.
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
    //removes every item whose name attribute equals John.
~~~
## Collection.addColumn(options, callback)

Adds a column to a specified collection.

* @param {object} options: Define the columns to be added.
* @param {function} callback: Function that handles the server's response

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

* @param {Query} _query: Query object that defines what item or set of items to remove. Optional
* @param {function} callback: Function that handles the server's response

**Basic example:**

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
    //This returns an object in the form of {count: 5}
~~~
**Example with query:**
~~~javascript
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
    //This returns an object in the form of {count: 2}

~~~

# Database

Class: ClearBlade.Database();

This class allows you to specify the query in raw SQL on the Platform and edge instead of using the existing ClearBlade query model. The function can be used for operations such as `JOIN` and `SOME` that are not supported by the ClearBlade query model.

Note: A raw query will not fire triggers or sync your changes.

Use the database object without the options object for performing query and exec operations on collections.

~~~javascript
		var db = ClearBlade.Database();
~~~

Use the database object with the external database options object to perform operations on external connections.

~~~javascript
		 var db = ClearBlade.Database({externalDBName: "externalDB"});
~~~

## ClearBlade.Database.query(_query, [params], callback)

This function returns query results to be parsed.
Params are optional.

~~~~javascript
	var db = ClearBlade.Database();
	var callback = function (err, data) {
        if (err) {
        	resp.error("Parse error : " + JSON.stringify(data));
        } else {
        	resp.success(data);
        }
	db.query("select sum(ingress) from traffic where entrance='Main_Entrance';", callback);
	//ingress is a traffic collection column
    };
~~~~

## ClearBlade.Database.exec(_query, [params], callback)

This function does not return query results. 
Params are optional.

* @param {Query} _query: Query object defining what operations are used
* @param {function} callback: Function that handles the server's response

~~~~javascript
	var db = ClearBlade.Database();
	var callback = function (err, data) {
        if (err) {
            resp.error("Parse error : " + JSON.stringify(data));
        } else {
            resp.success(data);
        }
    }
	db.exec("delete from traffic where entrance='Store_Entrance';", callback);
~~~~

## ClearBlade.Database.transaction(statements, callback)
Executes a SQL transaction with the given statements.
If one of the statements throws an error, the entire transaction is rolled back.
All statements are treated the same as exec, no results will be returned.

You can make use of the ClearBlade.Database.statement(statement, args...) helper function to simplify the building of a transaction.

~~~javascript
    var db = ClearBlade.Database();
    db.transaction([
        db.statement("UPDATE users SET email = $1 WHERE item_id = $2", "test@clearblade.com", 123),
        db.statement("INSERT INTO audit(user_id, action, time) VALUES ($1, $2, $3)", 123, "email updated", new Date()),
        db.statement("UPDATE assets SET owner_email = $1 WHERE owner_id = $2", "test@clearblade.com", 123),
    ]);
~~~

## ClearBlade.Database.performOperation(callback, arguments)

This function takes a callback as the first argument and a variable number of string arguments after the callback.

* @param {function} callback: Function that handles the server's response
* @param {...string} arguments: Arguments used in external databases.

We define the param arguments for each database below.

### MongoDB 

dbCommand: Valid MongoDB commands 

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

__The following cursor methods are supported:__
* sort
* limit
* skip
* collation

MongoDB CLI commands can be found here: https://docs.mongodb.com/manual/reference/method/js-collection/
  
~~~~javascript

    var db = ClearBlade.Database({externalDBName: "externalDB"});
	var dbCommand = 'db.collectionName.find()'
	var callback = function(err, data) {
  		if(err) {
   		 	resp.error("Error performing external db operation: " + JSON.stringify(data))
  		} else {
    		resp.success(data);
  		}
	};
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
	};
	db.performOperation(callback, sqlQuery1)
~~~~


~~~~javascript
// Another way to invoke the db.performOperation. This example is in continuation of the above example, changing values for a few variables.
var sqlQuery2 = "SELECT * from myTable where name=$1"
	db.performOperation(callback, sqlQuery2, "Bob") // 3rd arg will be substitution for $1
~~~~

### CouchDB

- arguments:
  - httpMethod: example "POST"
  - uri: example "/mydb/bulk_docs"
  - data: example: "{\"docs\": [{\"name\": \"Bob\", \"age\": 100}]}"
  `data` is optional and is unavailable for `GET` operations.

Please use the APIs listed here: https://docs.couchdb.org/en/stable/api/index.html

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
	};
	db.performOperation(callback, httpMethod, uri)
~~~~

~~~~javascript
// Another way to invoke the db.performOperation. This example is in continuation of the above example, changing values for a few variables.
	var httpMethod = "POST"
	var uri = "/myDb/bulk_docs"
	var data = {
		"docs": [{"name": "Bob", "age": 100}]
	}
	db.performOperation(callback, httpMethod, uri, JSON.stringify(data))
~~~~

### BigQuery 

- arguments:
  - operation
  - query

~~~javascript
var db = ClearBlade.Database({externalDBName: "myBigquery"});
var operation = "query";
var query = "SELECT name, gender, SUM(number) AS total FROM `bigquery-public-data.usa_names.usa_1910_2013` GROUP BY name, gender ORDER BY total DESC LIMIT 10";
var callback = function(err, data) {
    if(err) {
         resp.error("Error performing external db operation: " + JSON.stringify(data))
    } else {
        resp.success(data);
    }
};
db.performOperation(callback, operation, query)
~~~

~~~javascript
function testInsert(req,resp) {
    var db = ClearBlade.Database({externalDBName: "myBigquery"});
    var operation = "insert";
    var dataToInsert = {
        name: "John Doe",
        age: 25,
        created: new Date(),
    };
    var insertOptions = {
        dataset: "test",
        table: "testTable",
        data: dataToInsert,
    };
    var callback = function(err, data) {
        if(err) resp.error("Error inserting data in bigquery: " + JSON.stringify(data));
        resp.success(data);
    }
    db.performOperation(callback, operation, insertOptions);
}
~~~

## ClearBlade.Database.performOperationAsync(callback, argument)

This function is an asynchronous version of ```performOperation```. This function takes a callback as the first argument and a variable number of string arguments after the callback. This function does not return the operation results. It only returns ___"Success"___ if the operation succeeded or an error message if the operation failed.

* @param {function} callback: Function that handles the server's response.
* @param {...string} arguments: Arguments used in external databases. 


# Class: ClearBlade.Device(options)

To instantiate a device object, call:

~~~javascript

	var dev = ClearBlade.Device();
~~~

The available methods for the device class and examples of how to use them are listed below.

## Device.fetch(query, callback)

Requests a device or a set of devices from the devices collection.

* @param {Query} _query Used to request a specific device or subset of devices from the server's devices collection. Optional. If a query is not specified, __ALL__ devices will be returned.
* @param {function} callback Supplies processing for what to do with the data that is returned from the collection.

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

* @param {object} device A JSON object representing the device to be created. The attributes specified in the JSON object correspond to the columns in the Devices table, including any custom columns. They will be used to initialize the contents of the respective column.
* @param {function} callback Supplies processing for what to do with any returned data when the invocation is completed.

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
* @param {object} changes A JSON object representing the device columns to be updated. The attributes specified in the JSON object correspond to the columns in the devices table, including any custom columns. They will be used to update the contents of the respective column.
* @param {function} callback Supplies processing for what to do with any returned data when the invocation has been completed.

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

* @param {Query} _query Used to delete a specific device or subset of devices from the server's devices collection. Required. If a query is not specified, an error will be returned, preventing accidental deletion of all devices.
* @param {function} callback Supplies processing for what to do with any returned data when the invocation is completed.

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

**Please note: we recommend using the [MQTT library](https://github.com/ClearBlade/native-libraries/blob/master/mqtt.md) instead of the messaging methods.**

Class: ClearBlade.Messaging()

To instantiate a messaging object, call:

~~~javascript

	var msg = ClearBlade.Messaging();
~~~

The available methods for the messaging class and examples of how to use them are listed below.

## Messaging.getMessageHistory(topic, start, count, callback)

Retrieves the message history for a topic within the specified parameters.

* @param {string} topic: String that signifies which topic to search
* @param {number} start: Epoch timestamp in seconds that will retrieve 'count' number of messages after that timestamp
* @param {number} count: Number that signifies how many messages to return; 0 returns all messages
* @param {function} callback: Function that handles the server's response

**Example:**

~~~javascript
	var msg = ClearBlade.Messaging();
    var unixTimeMilli = new Date().getTime()
    // Get messages from the last 60 seconds
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

Subscribes to an MQTT message topic.

Prereq: The user's role should have permission to subscribe to that topic. Verify on the `Roles` page of `ClearBlade Console`.

Callback subscribe:

* @callback subscribeCallback
* @param {boolean} err: Is true if an error exists.
* @param {Object} data: The response from the MQTT broker.


Function subscribe:

* @param {string} topic: String that signifies which topic to subscribe
* @param {function} subscribeCallback: Function that handles the response from the MQTT broker

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

This method waits for a message on the topics array it is provided. Usually used within stream services.

Callback waitForMessage:

 * @callback waitForMessageCallback
 * @param {boolean} err: Is true if an error exists
 * @param {string} msg: The message which gets published on the topic
 * @param {string} topic: One of the topics that waitForMessage was listening on


Function waitForMessage:

* @param {[]string} topics: An array of topics to wait for messages on
* @param {function} waitForMessageCallback: Function that handles the server's response

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

* @param {string} topic: String that signifies which topic to search
* @param {number} count: Number that signifies how many messages to return; 0 returns all messages
* @param {int} last: Epoch timestamp in seconds that will retrieve 'count' number of messages before that timestamp
* @param {int} start: Epoch timestamp in seconds that will retrieve 'count' number of messages within the timeframe
* @param {int} stop: Epoch timestamp in seconds that will retrieve 'count' number of messages within the timeframe
* @param {function} callback: Function that handles the server's response

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

* @param {string} topic: The topic path of the message to be published. This will be sent to all listeners on the topic. No default.
* @param {string} payload: The payload to be sent. Also no default.

{{< warning title="Topic Restrictions" >}}
A topic comprises one or more topic levels (ex., 'factory/1/device/3' is a single topic with four topic levels). The '/' is a reserved character to separate topic levels; thus, a topic level cannot contain '/' in its name. However, spaces and punctuation are allowed.
{{< /note >}}
**Example:**

~~~javascript
    var msg = ClearBlade.Messaging();
    msg.publish("ClearBlade/is awesome! Topic","ClearBlade totally rules");
~~~

## Message.getCurrentTopics(callback)

Returns a system's current topics

* @param {function} callback: Function that handles the server's response

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

 This call deletes the MQTT topic's message history. It has four options to set when you delete it. 'count' is the number of messages you wish to delete. For count = 0, all messages for that topic are deleted. You can also use the last or start and stop options to widen your search. The last option, an epoch timestamp in seconds, will retrieve and delete 'count' number of messages before that timestamp. The start and stop options, an epoch timestamp, will retrieve and delete the 'count' number of messages within that timeframe. For each case, you can set 'count' to zero or a non-zero value depending on how many messages you wish to delete.

 The return value is a list of objects, one for each returned item in the message history.

 * @param {string} topic: String that signifies which topic to search
 * @param {number} count: Number that signifies how many messages to return and delete; 0 returns and deletes all messages
 * @param {int} last: Epoch timestamp in seconds that will retrieve and delete 'count' number of messages before that timestamp
 * @param {int} start: Epoch timestamp in seconds that will retrieve and delete 'count' number of  messages within timeframe
 * @param {int} stop: Epoch timestamp in seconds that will retrieve and delete 'count' number of  messages within timeframe
 * @param {function} callback: Function that handles the server's response

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
    msg.getAndDeleteMessageHistory("TestTopic", 0, null, null, null, callback); // get and delete all messages for TestTopic.
~~~

## messaging.setTimeout(timeout, topic, data, callback)

This method publishes data to a topic after timing out.

@callback cbSetTimeout
 * @param {boolean} err: Is true if an error exists
 * @param {string} msg: timerId if no error, error message otherwise

function setTimeout:
 * @param {number} timeout: Timeout in milliseconds
 * @param {string} topic: String that signifies which topic to search
 * @param {string} data: Data to publish to the topic once it timeouts
 * @param {cbSetTimeout} callback: Function that handles the server's response



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

 * @param {function} callback: Function that handles the server's response
 * @param {boolean} err: Is true if an error exists
 * @param {string} timerId: If there is no error, error message otherwise

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

This method publishes data to a topic after timing out and repeats iterations.

@callback cbSetInterval:
 * @param {boolean} err: Is true if an error exists
 * @param {string} msg: timerId if no error, error message otherwise


function setInterval:

 * @param {number} timeout: Timeout in milliseconds
 * @param {string} topic: The topic to publish to after every timeout
 * @param {string} data: Data to publish to the topic once it timeouts
 * @param {number} iterations: Number of times the timeout is set
 * @param {function} callback: Function that handles the server's response

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

 * @param {function} callback: Function that handles the server's response
 * @param {boolean} err: Is true if an error exists
 * @param {string} intervalId: If there is no error, error message otherwise

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

The code class calls other code services from within a service.
To instantiate the code class call:

~~~javascript
	var code = ClearBlade.Code();
~~~

## Code.execute(name, params, loggingEnabled, callback)

Executes another code service.

~~~javascript
/**
* @param {string} name: String representing the code service name that should be executed.
* @param {Object} params: Object containing all the parameters used for the code service. Optional.
* @param {Boolean} loggingEnabled: Boolean that represents whether or not the code service should use logging.
* @param {callback} Handles the code service's response.
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
Accepts a ClearBlade.Query object and a callback. It will return all users that match the query (except attempting to match the password field).

## User.setUsers(query,changes,callback)  
Operates on the users table in the same way an update works on a collection.

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
Updates the user the service is running as.

## User.count(query,callback)  
Returns a user count who have matched the query.

## Timer

Events: ClearBlade Timer


## ClearBlade.Timer.Create(name, options, callback)
### Create a new timer (runs a function at a set interval)
```javascript
ClearBlade.Timer.Create("runValidatorEveryHour",
    {
        frequency: 3600,
        service_name: "validator",
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
### Fetch an existing timer


## timer.Update(options, callback)
```javascript
ClearBlade.Timer.Fetch("runValidatorEveryHour", function (err, timer) {
        if (err) {
            resp.error(response);
        } else {
            timer.Update({ frequency: 600 }, function (err, response){
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
### Create a new trigger (runs a service after a defined action is detected)
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
### Fetch an existing trigger


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

Returns true if the code service is executing on an edge. Otherwise, false.

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

Returns the edge name if running on an edge, else returns an empty string.

~~~javascript
function myService(req, resp){
	ClearBlade.init({request:req})
	var edgeId = ClearBlade.edgeId()
	resp.success("Edge Id: " + edgeId)
}
~~~
# Roles 

Class: ClearBlade.Roles()

This class allows for interaction with roles, settings, and information.
To instantiate the roles class call:

~~~javascript
	var roles = ClearBlade.Roles();
~~~
## roles.create(roleMeta, callback)

This method creates a role.

* @param {Object} roleMeta
* @param {string} roleMeta.name
* @param {string} roleMeta.description
* @param {callback} callback  

* @callback callback
* @param {boolean} err
* @param {string} data: Returns role info from the requested roleID or error string

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
    var callback = function (err, data) {
        if (err) {
            resp.error(data);
        } 
        resp.success(data);
    }
    roles.create({"name":"newRole","description": "This is a new role!"}, callback);
~~~
## roles.update(roleID, changes, callback)

This method updates a role's description. To update the permissions, check the permissions class.

* @param {string} roleID: Required.
* @param {Object} changes
* @param {string} changes.description: Updated role description
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns role info from the requested roleID or error string

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.update("roleID",{"description": "This is a new description!"}, callback);
~~~

## roles.delete(roleID, callback)

This method deletes a role.

* @param {string} roleID: The role's unique ID. Required.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns role info from the requested roleID or error string

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.delete("roleID", callback);
~~~
## roles.get(query, callback)

This method gets role info with a query.

* @param {Query} query: Query object that filters the roles. Query page size will default to 100 if the size is not provided
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns role info from requested roleID or error string

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.get(query, callback);
~~~

## roles.getById(roleID, callback)

* This method gets role info with the role ID.
* @param {string} roleID: Required.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns role info from the requested roleID or error string

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.getById("roleID", callback);
~~~

## roles.getByName(roleName, callback)

This method gets role info with the role name.

* @param {string} roleName
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns role info for the requested roleName or error string

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.getByName("roleName", callback);
~~~

## roles.addRoleToUser(userID, roleNameOrID, callback)

This method adds a role to a user.

* @param {string} userID
* @param {string} roleNameOrID: A role can be added to the user using a role name or ID
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns a value for `user_role_id`

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.addRoleToUser("userID","roleNameOrID", callback);
~~~

## roles.getRolesForUser(userID, callback)

This method gets role(s) for a user.

* @param {string} userID
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns the IDs for role, user, and user_role

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.getRolesForUser("userID", callback);
~~~

## roles.removeRoleFromUser(userID,roleNameOrID, callback)

This method removes a role for a user.

* @param {string} userID 
* @param {string} roleNameOrID: A role can be removed from the user using a role name or ID
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns a success response

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.removeRoleFromUser("userID","roleName", callback);
~~~

## roles.addRoleToDevice(deviceName,roleNameOrID, callback)

This method adds a role to a device.

* @param {string} deviceName 
* @param {string} roleNameOrID: A role can be added to the device using a role name or ID
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns unique `device_role_id`

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.addRoleToDevice("deviceName","roleName", callback);
~~~

## roles.getRolesForDevice(deviceName, callback)

This method gets role(s) for a device.

* @param {string} deviceName 
* @param {callback} callback 

* @callback callback
* @param {boolean} err
* @param {string} data: Returns the IDs for role, device, and device_role

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.getRolesForDevice("deviceName", callback);
~~~

## roles.removeRoleFromDevice(deviceName,roleNameOrID, callback)

This method removes a role for a device.

* @param {string} deviceName 
* @param {string} roleNameOrID: A role can be removed from the device using a role name or ID
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    roles.removeRoleFromDevice("deviceName","roleName", callback);
~~~

## roles.duplicateRole(newRoleInfo,oldRoleID, callback)

This method duplicates an existing role's meta and permissions.

* {Object} newRoleInfo
* {string} newRoleInfo.name
* {string} newRoleInfo.description
* @param {string} oldRoleID 
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns a new roleID for the duplicate role

### Example

~~~javascript
    ClearBlade.init({request:req});
    var roles = ClearBlade.Roles();
    var callback = function (err, oldRoleID) {
        if (err) {
            resp.error(oldRoleID);
        } 
        resp.success(oldRoleID);
    }
    roles.duplicateRole({"name":"duplicateRole","description": "This is a new role!"},"oldRoleID",callback);
~~~

# Permissions

Class: ClearBlade.Permissions()

This class allows for interaction with permission settings and information.
To instantiate the permissions class call:

~~~javascript
	var permissions = ClearBlade.Permissions();
~~~

### Permission values

|`resourceType`|`requestType`|
|-----|------|
|service|1 = READ|
|allservices|2 = CREATE|
|collection|4 = UPDATE|
|allcollections|8 = DELETE|
|allexternaldatabases|
|topic|
|messagehistory|
|users|
|dashboard|
|devices|
|edges|
|deployments|
|timers|
|triggers|
|roles|
|servicecache|
|externaldatabase|
|manageusers|

Example for `requestType`:

1 + 2 = 3, which is the READ and CREATE permissions. 15 is all permissions

## permissions.addPermissionToRole(roleID, info, callback)

This method adds permissions to a role.

* @param {string} roleID: The role's unique ID.
* {Object} info
* {int} info.requestType: Permission types to be added. See above for possible values.
* {string} info.resourceType: Asset types that the permissions are added to. See above for possible values.
* {string} info.resourceName: Asset name to add permissions to.
* @param {string} oldRoleID 
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns permID

### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
    var callback = function (err, data) {
        if (err) {
            resp.error(data);
        } 
        resp.success(data);
    }
    permissions.addPermissionToRole("roleID",{"requestType": 3,"resourceType": "allservices", "resourceName":""}, callback);
~~~

## permissions.getPermissionsForRole(roleID, callback)

This method gets all permissions for a role.

* @param {string} roleID: Required.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns all permissions and info for the asset(s) with the roleID

### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    permissions.getPermissionsForRole("<roleID>", callback);
~~~

## permissions.getPermissionsForResource(resourceType, resourceName, callback)

This method gets all permissions for a resource.

* @param {string} resourceType: Asset types that have the permissions. See above for possible values.
* @param {string} resourceName: The asset's name has the permissions.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data: Returns all permission info and role ID for the asset(s) with the resource name

### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    permissions.getPermissionsForResource("service","serviceName", callback);
~~~

# permissions.increasePermissionsForRole(roleID, permID, requestType, callback)

This method increases existing permissions for roles and resources.

* @param {string} roleID: The role's unique ID 
* @param {string} permID: The permission's ID
* @param {int} requestType: Permissions types to be added. See above for possible values.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data 

### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    permissions.increasePermissionsForRole("roleID","permID",15, callback);
~~~

# permissions.decreasePermissionsForRole(roleID, permID, requestType, callback)

This method decreases existing permissions for roles and resources.

* @param {string} roleID 
* @param {string} permID 
* @param {int} requestType: Permissions types to be added. See above for possible values.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data 

### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    permissions.decreasePermissionsForRole("roleID","permID",1, callback);
~~~

## permissions.removePermissionsForRole(roleID, callback)

This method removes all the permissions for a role.

* @param {string} roleID
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data 

### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    permissions.removePermissionsForRole("<roleID>", callback);
~~~

## permissions.removePermissionsForResource(resourceType, resourceName, callback)

This method removes all the permissions for a resource.

* @param {string} resourceType: Asset types that have the permissions. See above for possible values.
* @param {string} resourceName: The asset's name has the permissions.
* @param {callback} callback

* @callback callback
* @param {boolean} err
* @param {string} data


### Example

~~~javascript
    ClearBlade.init({request:req});
    var permissions = ClearBlade.Permissions();
	var callback = function (err, data) {
			if (err) {
				resp.error("Parse error : " + JSON.stringify(data));
			} else {
				resp.success(data);
			}
		}
    permissions.removePermissionsForResource("service","serviceName", callback);
~~~

# Lock

Class: ClearBlade.Lock()

This class allows for interaction with cache locks. If the lock is used across multiple services, they must all use the same name.

To instantiate the permissions class call:

~~~javascript
	var myLock = ClearBlade.Lock(name,caller);
~~~

 * @param {string} name
 * @param {string} caller
 * @returns {Lock}

### Example

~~~ javascript
function incrWithLock(req, resp) {
  ClearBlade.init({request: req});
  var cache = ClearBlade.Cache(“IncrCache”);
  var myLock = ClearBlade.Lock(“CacheLock”, “service incrWithLock”);
  for (i = 0; i < 100; i++) {
  	myLock.lock();
 	cache.get(“incrVal”, function(err, data) {
  		if (err) {
 			resp.error(“Could not get incrVal: ” + JSON.stringify(data));
	        };
		cache.set(“incrVal”, data + 1, function(serr, sdata) {
			if (serr) {
				resp.error(“Could not set incrVal: ” + JSON.stringify(sdata));
 	    	});
 		});
 	});
	myLock.unlock();
  }
 resp.success(“Incremented incrVal 100 times”);
}
~~~

## myLock.lock()

This method obtains a write lock on the entire cache.

## myLock.unlock()

This method releases the lock.

## myLock.rlock()

This method obtains a read lock for multiple users.

## myLock.runlock()

This method obtains releases the read lock.

# Deployments

This class is used for interacting with deployments. You can create, delete, update, or
read deployments.

To instantiate a deployment object, call:

~~~ javascript
    var depl = ClearBlade.Deployment();
~~~

## Deployment.create(name, description, options, callback)

Creates a new deployment

* @param {string} name The deployment's name.
* @param {string} description Describes the deployment's purpose.
* @param {object} options Specifies the deployment's assets and edges.
* @param {function} callback Supplies processing for what to do with any data supplied when the invocation completes.

The options parameter is a JSON object specifying the deployment's assets and edges. The structure
is as follows:

~~~ json
[
  {
    "assets": [
        {
            "asset_class": "services",
            "asset_id": "",
            "sync_to_edge": true,
            "sync_to_platform": false
        },
        {
            "asset_class": "devices",
            "asset_id": "myDevice1",
            "sync_to_edge": true,
            "sync_to_platform": true
        },
        ...
    ],
    "edges": [
        "edge1Name",
        "edge2Name",
        ...
        "edgeNname"
    ]
  }
]
~~~

If asset_id == "", all assets of that type are synced as specified. If you
want both sync_to_edge and sync_to_platform to be true, you can specify,
"sync": true.

The types of assets that can be deployed (asset_class) are specified in the ClearBlade
documentation online in the Asset subheading in the Deployment section.

Example:

~~~ javascript
	ClearBlade.init({request: req});

	var options = {
        "assets": [
            {
                "asset_class": "services",
                "asset_id": "",
                "sync_to_edge": true,
                "sync_to_platform": false
            },
            {
                "asset_class": "devices",
                "asset_id": "myDevice1",
                "sync_to_edge": true,
                "sync_to_platform": true
            }
        ],
        "edges": [
            "edge1Name",
            "edge2Name",
            "edgeNname"
        ]
      };

    var depl = ClearBlade.Deployment();

	depl.create(name, description, options, function(err, data) {
		if(err){
			resp.error("Unable to create deployment: " + JSON.stringify(data))
		}
		resp.success(data);
	});

~~~

The createDeployment() call returns the deployment object on success. For example:

~~~ json
    {
        "name": "<deployment name>",
        "description": "<deployment description>",
        "assets": [
            {
                "asset_class": "services",
                "asset_id": "",
                "sync_to_edge": true,
                "sync_to_platform": false
            },
            {
                "asset_class": "devices",
                "asset_id": "myDevice1",
                "sync_to_edge": true,
                "sync_to_platform": true
            }
        ],
        "edges": [
            "edge1Name",
            "edge2Name",
            "edgeNname"
        ]
      };

~~~

## Deployment.read(name, callback)

Reads one deployment

* @param {string} name The deployment name.
* @param {function} The processing to do with the returned deployment information.

Example:

~~~ javascript
	ClearBlade.init({request: req});

    var depl = ClearBlade.Deployment();

	depl.read(name, function(err, data) {
		if(err){
			resp.error("Unable to read deployment: " + JSON.stringify(data))
		}
		resp.success(data);
	});
~~~

Deployment.read returns the requested deployment. It is structured identically to the
return value of createDeployment() above.

## Deployment.readAll(query, callback)

Reads zero or more deployments based on the query parameter

* @param {Query} query A query object that filters the deployments that are passed to the callback.
* @param {function} callback The function that processes the query results.

Example:

~~~ javascript
	ClearBlade.init({request: req});

    var depl = ClearBlade.Deployment();

	depl.readAll(query, function(err, data) {
		if(err){
			resp.error("Unable to read deployments: " + JSON.stringify(data))
		}
		resp.success(data);
	});

~~~

Deployment.readAll return an array of deployments, where each deployment has the exact
structure as in Deployment.create() and Deployment.read().

## Deployment.update(name, changes, callback)

Updates the options (edges, assets) of a deployment.

* @param {string} name The deployment name.
* @param {object} changes Specifies the changes (assets, edges) to the deployment.
* @param {function} callback Processing to perform when the update completes.

The changes parameter is an object. It contains an optional new description, changed assets,
and changed edges. It looks as follows:

~~~ json
    {
        "description": "<optional new deployment description>",
        "assets": {
            "add": [
                {
                    "asset_class": "services",
                    "asset_id": "",
                    "sync_to_edge": true,
                    "sync_to_platform": false
                }
            ],
            "remove": [
                {
                    "asset_class": "devices",
                    "asset_id": "myDevice1"
                }
            ],
        },
        "edges": {
            "add": [
                "newEdge1Name",
                "newEdge2Name"
            ],
            "remove": [
                "edgeToGoAway"
            ]
         }
      };
~~~

## Example:

~~~ javascript
	ClearBlade.init({request: req});

    var depl = ClearBlade.Deployment();

    var changes = {<object similar to the one shown above>}

	depl.update(name, changes, function(err, data) {
		if(err){
			resp.error("Unable to update deployment: " + JSON.stringify(data))
		}
		resp.success(data);
	});

~~~

Deployment.update() returns the deployment with all the changes applied. The
structure of the return value is the same as in Deployment.create() above.

## Deployment.delete(name, callback) 

Deletes a deployment.

* @param {string} name The deployment name
* @param {function} callback Processing to be performed when the deployment is deleted.

## Example:

~~~ javascript
	ClearBlade.init({request: req});

    var depl = ClearBlade.Deployment();


	depl.delete(name, function(err, data) {
		if(err){
			resp.error("Unable to delete deployment: " + JSON.stringify(data))
		}
		resp.success(data);
	});

~~~

There is no return value other than if an error occurs.


## Code

## ClearBlade.Code.getAllServices(callback)
Retrieves a list of code service names in your system.

```javascript
ClearBlade.Code().getAllServices(function(isError, response) {
    if (isError) resp.error(response);
    else {
        resp.success("all code services in my system: " + JSON.stringify(response))
    }
})
```

### ClearBlade.Code.execute(name, params, loggingEnabled, callback)
Executes the specified code service.

```javascript
ClearBlade.Code().execute("myService", {some_param: 123}, true, function(isError, response) {
    if (isError) resp.error(response);
    else {
        resp.success("response from service myService: " + JSON.stringify(response))
    }
})
```
