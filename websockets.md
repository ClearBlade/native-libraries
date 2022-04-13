This library provides an API to connect to a Websocket server from a code service and provides read/write asynchronous functionality. 

## WebSocket.Client(options)

Client represents a Websocket client connected to a server.

* @param {object} [options] - required. Specifies the websocket connection options
* @param {string} [options.url] - required. Destination server url
* @param {string} [options.username] - optional. If specified username and password will be added to the HTTP Basic Auth header
* @param {string} [options.password] - optional
* @param {int} [options.version] - optional. Default is 13
* @param {Array\<string\>} [options.protocols] - optional. Protocols to use while establishing a connection
* @param {function} [options.onMessage] - required. A callback for handling received messages
* @param {function} [options.onConnLost] - required. A callback for handling connection disconnect
* @param {boolean} [options.useTLS=false] - optional. Default is false
* @param {object} [options.headers] - optional. Specify any additional headers
* @constructor

Example

The code block below is to create a new websocket client. Throws an error on failure. 

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

This function connects to the websocket server and spawns a websocket read in the background. This is a blocking function which returns after a successful connection or throws an error on failure.

~~~ javascript
try {
  client.connect();
} catch(e) {
  // handle error
}
~~~
  
## Client.write(data)
  
This function writes data to the websocket. 
  
* @param {Uint8Array|string} [data] - required
* @returns {Promise}

~~~ javascript
client.write("hello").then(function() {
    // do something here
}).catch(function(reason) {
  resp.error("Failed to write to websocket: " + reason);
});
~~~

## Client.close()
  
This function closes the websocket connection. 
 
* @returns {Promise}

~~~ javascript
client.close().then(function() {
    // do something here
}).catch(function(reason) {
  resp.error("Failed to close websocket: " + reason);
});
~~~


