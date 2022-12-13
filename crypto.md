# crypt.crypto
The crypto module offers a way of encapsulating secure credentials.

* crypto.createHash()
* crypto.update()
* crypto.digest()
* crypto.encode()
* crypto.decode()
* crypto.hmac()


## crypto.createHash(hashName)		

Creates and returns a hash object, which can be used to generate hash digests.
Supported hash types for "sha1","md5","sha256","sha512".
	
**Example:**
~~~javascript
	var sha1 = crypto.createHash("sha1");	
~~~

## crypto.update(data)
Updates the hash content with the given data. 

**Example:**
~~~javascript
	var sha1 = crypto.createHash("sha1");
	sha1.update("Hello World");
~~~

## crypto.digest(encodingType)
	
Calculates the digest of all of the passed data to be hashed. The encoding can be "binary","base64" or "hex".

**Example:**
~~~javascript
	var sha1 = crypto.createHash("sha1");
	sha1.update("Hello World");
	var hash = sha1.digest("hex");
~~~

## crypto.encode(encodingType, data)

Encodes data depending on the encoding type. 
Encoding types currently supported are 'base64' and 'hex'.
Data may be either a string or a Uint8Array.

**Example:**
~~~javascript
	var encData = crypto.encode("base64", "myData");
~~~

## crypto.decode(decodingType, encodedData)

Decodes data depending on the encoding type.
Decoding types currently supported are 'base64' and 'hex'.
Encoded data may be either a string or a Uint8Array.
Returns a Uint8Array.

**Example:**
~~~javascript
	var encodedData = "aGVsbG8=";
	var decData = crypto.decode("base64", encodedData);
~~~

## crypto.hmac(data, key, hashType)

Returns the hmac signature of the supplied data. 
The only hash type currently supported is "sha256".
Data and key may be either strings or Uint8Arrays.
Returns a Uint8Array.

**Example:**
~~~javascript
    var data = "hello world";
    var key = "my key";
    var hmac = crypto.hmac(data, key, "sha256");
~~~

