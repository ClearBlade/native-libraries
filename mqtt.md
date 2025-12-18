This library allows for the execution of async ClearBlade messaging functions by providing an asynchronous MQTT client. You can connect to ClearBlade's Internal Broker for the same system where the code resides or an external broker (e.g., ClearBlade Broker in another system on the same or different instance, Mosquitto Broker, etc.)
## MQTT.Message(payload)

The message represents an MQTT message.

* @param {string|Uint8Array} payload
* @constructor
* @property {string} payload
* @property {Uint8Array} payload_bytes
* @property {number} qos
* @property {boolean} retain
* @property {boolean} duplicate
* @property {string} topic The topic that the message was published on
* @property {Record<string, string>} user_properties

Example

~~~javascript
var msg = new MQTT.Message("my message")
msg.user_properties["property_name"] = "property_value"
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
* @param {number} [options.keepalive=30] seconds
* @param {boolean} [options.use_tls=false]
* @param {object} [options.tls_config]
* @param {string} options.tls_config.client_cert
* @param {string} options.tls_config.client_key
* @param {string} options.tls_config.ca_cert
* @param {function} [options.on_connect]
* @param {function} [options.on_disconnect]
* @constructor

Example

The code block below is to connect to an external ClearBlade broker.

~~~javascript
var options = {
  address: 'platform.clearblade.com',
  port: 1884,
  use_tls: true,
  username: "<CB_TOKEN>",
  password: "<SYSTEM_KEY>",
}
var client = new MQTT.Client(options);
~~~

## Client.subscribe(topic, onMessage)

This function subscribes to a topic in the broker. It registers a callback function when a message on that topic is received.

* @param {string|string[]} topic The topic or array of topics to subscribe to
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

This function allows publish to send an MQTT message to the broker.

* publish sends an MQTT message to the broker.
* @param {string} topic The topic to publish to. If the payload is of type `Message`, this takes precedence over `payload.topic`.
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

## Client.createPublishStream(topic, qos, retain, user_properties)

This function returns a stream that can be used to write and publish a MQTT message

* @param {string} topic The topic to publish to.
* @param {number} [qos]
* @param {boolean} [retain]
* @param {Record<string, string>} MQTT 5 user properties
* @returns {WritableStream} Writes the contents of the MQTT payload. When closed, the message is published.