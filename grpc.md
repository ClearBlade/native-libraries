The ClearBlade gRPC library compiles proto files at runtime and provides a gRPC client. 

Streaming methods are not supported.

__Reference__
1. [Loading from a Bucket](#loading-from-a-bucket)
2. [Metadata](#metadata)

__Examples__
1. [Hello World Example](#hello-world-example)
2. [Metadata Example](#metadata-example)

# Reference

## Loading From a Bucket

~~~javascript
    /**
     * Represents a loaded proto file
     * @typedef {{[packageName: string]: GrpcPackage}} GrpcObject
     */

    /**
     * Represents a proto file pacakge
     * @typedef {{[serviceName: string]: ServiceConstructor}} GrpcPackage
     */

    /**
     * Constructs a connection to a gRPC service
     * @typedef {new (address: string) => ServiceClient} ServiceConstructor
     */

    /**
     * A gRPC service client that can invoke methods for the service
     * @typedef {{[methodName: string]: GrpcMethod}} ServiceClient
     * @property {() => void} close Closes the current grpc connection. It cannot be opened again.
     */

    /**
     * gRPC method overloads
     * @typedef {(data: Object, callback: GrpcCallback) => void} UnaryRequest
     * @typedef {(data: Object, metadata: Metadata, callback: GrpcCallback) => void} UnaryRequestWithOptions
     * @typedef {UnaryRequest | UnaryRequestWithOptions} GrpcMethod
     */

    /**
     * Loads a grpc proto file from a bucket set and returns a GrpcObject.
     * 
     * @param {string} bucketName BucketSet bucket name to load the proto file from
     * @param {string} bucketBox The box within the bucket set to load the file from
     * @param {string} filePath The path of the proto file relative to the box
     * @returns {GrpcObject}
     */
    grpc.loadDefinitionFromBucket(bucketName, bucketBox, filePath)
~~~

## Metadata

~~~javascript
    /**
     * Creates a metadata object
     * @returns {Metadata} Metadata
     */
    new grpc.Metadata()

    /**
     * @typedef {Object} Metadata
     * @property {(key: string, value: string) => void} set
     * @property {(key: string) => void} remove
     * @property {() => {[key: string]: string}} getMap
     */
    Metadata.set('key', 'value')
    Metadata.remove('key')
    Metadata.getMap()


~~~

## Hello World Example
This example assumes that it is running in a system with a bucket set named `protofiles` containing `helloworld.proto` in the `sandbox` box. The example protofile can be found [here](https://github.com/grpc/grpc/blob/master/examples/protos/helloworld.proto)

~~~javascript
    const def = grpc.loadDefinitionFromBucket("protofiles", "sandbox", "hello.proto")
    var hello_proto = def.helloworld
    var greeter = new hello_proto.Greeter("grpc_server:50051");
    greeter.SayHello({name: "ClearBlade"}, function(err, response) {
        resp.success('Greeting: ' + response.message);
    });
~~~

## Protobuf Encode / Decode Examples
These examples assume that the code is running in a system with a bucket set named `protofiles` containing the following `person.proto` in the `sandbox` box:

```
syntax = "proto2";

message Address {
    optional string street = 1;
    optional string city = 2;
}

message Person {
    optional string name = 1;
    optional int32 age = 2;
    optional Address address = 3;
}
```

~~~javascript
    // Protobuf encode
    const def = grpc.loadDefinitionFromBucket("protofiles", "sandbox", "person.proto");
    const personMsg = def.messages.Person;
    const personObj = {
        name: "John Doe",
        age: 30,
        address: {
            street: "123 Main St",
            city: "Springfield"
    }};
    personMsg.encode(person).then(function(encoded) {
        resp.success("encodedPerson:", Array.from(encoded))
    });
~~~

The response is ```encodedPerson: [10,8,74,111,104,110,32,68,111,101,16,30,26,26,10,11,49,50,51,32,77,97,105,110,32,83,116,18,11,83,112,114,105,110,103,102,105,101,108,100]```


~~~javascript
    // Protobuf decode
    const def = grpc.loadDefinitionFromBucket("protofiles", "sandbox", "person.proto");
    const personMsg = def.messages.Person;
    const encodedPerson = [16,30,26,26,10,11,49,50,51,32,77,97,105,110,32,83,116,18,11,83,112,114,105,110,103,102,105,101,108,100,10,8,74,111,104,110,32,68,111,101];
    personMsg.decode(new Uint8Array(encodedPerson)).then(function(personObj) {
        resp.success(personObj)
    });
~~~

The response is ```{"name": "John Doe", "age": 30, "address": { "city": "Springfield", "street": "123 Main St"}}```


## Metadata Example
This example shows how the metadata object can be used with a request

~~~javascript
    const def = grpc.loadDefinitionFromBucket("protofiles", "sandbox", "hello.proto")
    var hello_proto = def.helloworld
    var greeter = new hello_proto.Greeter("grpc_server:50051");


    const metadata = new grpc.Metadata()
    metadata.set('authorization', 'Bearer ' + ACCESS_TOKEN)

    greeter.SayHello({name: "ClearBlade"}, metadata, function(err, response) {
        resp.success('Greeting: ' + response.message);
    });
~~~