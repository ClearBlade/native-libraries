# crypt.crypto
The crypto module can encapsulate secure credentials.

* crypto.createHash()
* crypto.update()
* crypto.digest()
* crypto.encode()
* crypto.decode()
* crypto.hmac()


## crypto.createHash(hashName)		

Creates and returns a hash object, which can be used to generate hash digests.
Sha1, md5, sha256, and sha512 hash types are supported.
	
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
	
Calculates the digest of all of the passed data to be hashed. The encoding can be binary, base64, or hex.

**Example:**
~~~javascript
	var sha1 = crypto.createHash("sha1");
	sha1.update("Hello World");
	var hash = sha1.digest("hex");
~~~

## crypto.encode(encodingType, data)

Encodes data depending on the encoding type. 
Encoding types supported are base64 and hex.
Data may be a string or a Uint8Array.

**Example:**
~~~javascript
	var encData = crypto.encode("base64", "myData");
~~~

## crypto.decode(decodingType, encodedData)

Decodes data depending on the encoding type.
Decoding types supported are base64 and hex.
Encoded data may be a string or a Uint8Array.
Returns a Uint8Array.

**Example:**
~~~javascript
	var encodedData = "aGVsbG8=";
	var decData = crypto.decode("base64", encodedData);
~~~

## crypto.hmac(data, key, hashType)

Returns the supplied data's HMAC signature. 
The only hash type supported is sha256.
Data and keys are strings or Uint8Arrays.
Returns a Uint8Array.

**Example:**
~~~javascript
    var data = "hello world";
    var key = "my key";
    var hmac = crypto.hmac(data, key, "sha256");
~~~

## crypto.create_jwt(claims, algorithm, privateKey)

Assembles and signs a JWT with the given claims.
Claims is an object. Algorithm and privateKey are strings.
The only algorithms supported are RS256, ES256, and HS256.
Returns a string.

**Example**
~~~javascript
    var privateKey = "-----BEGIN EC PRIVATE KEY-----\nREDACTED obviously but put your real key here or this won't work\n-----END EC PRIVATE KEY-----";
    var claims = {
        sk: cbmeta.system_key,
        iat: (new Date() / 1000), // current Unix seconds
        exp: (new Date() / 1000) + 3600, // current Unix seconds + 1 hour
        uid: "test-device", // device name
        ut: 3, // user type = device
    }
    var token = crypto.create_jwt(claims, "ES256", privateKey);
~~~
