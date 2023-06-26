This library provides an API to connect to a WebSocket server from a code service and provides read/write asynchronous functionality. 

## WebSocket.Client(options)

The client represents a WebSocket client connected to a server.

* @param {object} [options]: Required. Specifies the WebSocket connection options.
* @param {string} [options.url]: Required. Destination server URL
* @param {string} [options.username]: Optional. The username and password will be added to the HTTP Basic Auth header if specified.
* @param {string} [options.password]: Optional
* @param {int} [options.version]: Optional. Default is 13
* @param {Array\<string\>} [options.protocols]: Optional. Protocols to use while establishing a connection
* @param {function} [options.onMessage]: Required. A callback for handling received messages
* @param {function} [options.onConnLost]: Required. A callback for handling connection disconnect
* @param {boolean} [options.useTLS=false]: Optional. Default is false
* @param {object} [options.headers]: Optional. Specify any additional headers
* @constructor

The code block below creates a new WebSocket client and throws an error on failure. 

~~~javascript
var onMessage = function(payload_bytes) {
  // do something with payload
  // payload_bytes is a Uint8Arrray
}
  
var onConnLost = function(reason) {
  // do something here
  // reason is a string
}

var options = {
  url: 'wss://mywebsocketserver.com',
  useTLS: true,
  username: "mySuperSecretUsername",
  password: "SuperSecurePassword",
  onMessage: onMessage,
  onConnLost: onConnLost,
  protocols: ["protocol1", "protocol2"],
  headers: {
    "Content-Type": ["application/json"]
  }
}
var client = new WebSocket.Client(options);
~~~

## Client.connect()

This function connects to the WebSocket server and spawns a WebSocket read in the background. This blocking function returns after a successful connection or throws an error on failure.

~~~ javascript
try {
  client.connect();
} catch(e) {
  // handle error
}
~~~
  
## Client.write(data)
  
This function writes data to the WebSocket. 
  
* @param {Uint8Array|string} [data]: required
* @returns {Promise}

~~~ javascript
client.write("hello").then(function() {
    // do something here
}).catch(function(reason) {
  resp.error("Failed to write to WebSocket: " + reason);
});
~~~

## Client.close()
  
This function closes the WebSocket connection. 
 
* @returns {Promise}

~~~ javascript
client.close().then(function() {
    // do something here
}).catch(function(reason) {
  resp.error("Failed to close WebSocket: " + reason);
});
~~~


