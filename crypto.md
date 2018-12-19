# crypt.crypto
The crypto module offers a way of encapsulating secure credentials.

* crypto.createHash()
* crypto.update()
* crypto.digest()
	

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
