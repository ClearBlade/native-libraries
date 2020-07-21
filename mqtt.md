This library allows for the execution of async ClearBlade messaging functions by providing an MQTT client that will work asynchronously. You can connect to the internal ClearBlade broker or an external broker. 

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
var msg = new MQTT.Message("my message") 	
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

The code block below is to connect to an external clearblade broker. 

~~~javascript
var client = new MQTT.Client(options);
var options = {
  address: 'platform.clearblade.com',
  port: 1883,
  username: "<CB_TOKEN>",
  password: "<SYSTEM_KEY>",
}
~~~

## Client.subscribe(topic, onMessage)

This function subscribes to a topic in the broker and registers a callback function which is called when a message on that topic is received.

* @param {string} topic
* @param {function(topic: string, message: Message)} onMessage
* @returns {Promise}

~~~ javascript
var client = new MQTT.Client();
function data(topic, message){
  log("received message on topic "+topic+": "+message.payload)
  }
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