This library allows for the execution of async ClearBlade messaging functions by providing an MQTT client that will work asynchronously. You can connect to the ClearBlade's Internal Broker for the same system where the code resides, or to an external broker (e.g. ClearBlade Broker in another system on the same or different instance, Mosquitto Broker, etc.).
## MQTT.Message(payload)

The message represents an MQTT message.

* @param {string|Uint8Array} payload
* @constructor
* @property {string} payload
* @property {Uint8Array} payload_bytes
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
* @param {function} [options.on_connect]
* @param {function} [options.on_disconnect]
* @constructor

Example

The code block below is to connect to an external clearblade broker.

~~~javascript
var options = {
  address: 'platform.clearblade.com',
  port: 1883,
  username: "<CB_TOKEN>",
  password: "<SYSTEM_KEY>",
}
var client = new MQTT.Client(options);
~~~

## Client.subscribe(topic, onMessage)

This function subscribes to a topic in the broker and registers a callback function which is called when a message on that topic is received.

* @param {string} topic
* @param {function(topic: string, message: Message)} onMessage
* @returns {Promise}

~~~ javascript
function myStreamService(req, resp){

  var client = new MQTT.Client();
  function onMessage(topic, message) {
    log("received message on topic " + topic + ": " + message.payload)
  }
  client.subscribe("incoming/data/topic", onMessage)
    .catch(function (reason) {
      resp.error("failed to subscribe: " + reason.message)
    });
}
~~~

## Client.publish(topic, payload, qos, retain)

This function allows publish to send a MQTT message to the broker.

* publish sends an MQTT message to the broker.
* @param {string} topic
* @param {string|Message|Uint8Array} payload
* @param {number} [qos]
* @param {boolean} [retain]
* @returns {Promise}

~~~javascript
function myCodeService(req, resp){
    var client = new MQTT.Client();
    var deviceData = {
        "data": 90,
        "deviceID": "myDevice"
    }
    var info = "Data on device: " + deviceData.deviceID + " is " + deviceData.data;
    client.publish("device/info", info)
        .then(function (resolve) {
            log(resolve);
            resp.success("success");
        }, function (reason) {
            log("failed to publish device data " + deviceData + ": " + reason.message);
            resp.error('failure');
        });
}
  ~~~
