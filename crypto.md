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
	
Calculates the digest of all of the passed data to be hashed. The encoding can be "binary","base64", or "hex".

**Example:**
~~~javascript
	var sha1 = crypto.createHash("sha1");
	sha1.update("Hello World");
	var hash = sha1.digest("hex");
~~~

## crypto.encode(encodingType, data)

Encodes data depending on the encoding type. 
Encoding types supported are 'base64' and 'hex'.
Data may be a string or Uint8Array.

**Example:**
~~~javascript
	var encData = crypto.encode("base64", "myData");
~~~

## crypto.decode(decodingType, encodedData)

Decodes data depending on the encoding type.
Decoding types supported are 'base64' and 'hex'.
Encoded data may be a string or Uint8Array.
Returns a Uint8Array.

**Example:**
~~~javascript
	var encodedData = "aGVsbG8=";
	var decData = crypto.decode("base64", encodedData);
~~~

## crypto.hmac(data, key, hashType)

Returns the supplied data's HMAC signature. 
The only hash type supported is "sha256".
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
The only algorithms supported are "RS256", "ES256", and "HS256".
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


## crypto.importKey(format, keyData)
Imports a key from a given format.

* @param {string} format: The format of the key data. Currently "PEM" is the only supported format.
* @param {string} keyData: The key data.
* @returns {Promise<CryptoKey>} The key

**Example:**
~~~javascript
    crypto.importKey("PEM", "-----BEGIN EC PRIVATE KEY-----\nREDACTED obviously but put your real key here or this won't work\n-----END EC PRIVATE KEY-----").then(function (key) {
        // Do something with the key
    })
~~~

## crypto.generateKey(algorithm)
Generates a keypair using the supplied algorithm parameters.

* @param {KeyAlgorithmParams} algorithm: The keygen algorithm parameters. Supports EcKeyGenParams and RsaHashedKeyGenParams. See example.
* @returns {Promise<CryptoKeyPair>} The public private keypair.

**Example:**
~~~javascript
    /**
     * Subset of EcKeyGenParams
     * https://developer.mozilla.org/en-US/docs/Web/API/EcKeyGenParams
     * 
     * @typedef {Object} EcKeyGenParams
     * @property {"ECDH" | "ECDSA"} name
     * @property {"P-256" | "P-384" | "P-521"} namedCurve
     */
    crypto.generateKey({
        name: "ECDSA",
        namedCurve: "P-521"
    }).then(function (keypair) {
        // Do something with the keypair
    })

    /**
     * Subset of RsaHashedKeyGenParams
     * https://developer.mozilla.org/en-US/docs/Web/API/RsaHashedKeyGenParams
     * 
     * @typedef {Object} RsaHashedKeyGenParams
     * @property {"RSA"} name
     * @property {number} modulusLength
     */
    crypto.generateKey({
        name: "RSA",
        modulusLength: 4096
    }).then(function (keypair) {
        // Do something with the keypair
    })
~~~

## crypto.exportKey
Exports a key to the specified format

* @param {"PEM"} format
* @param {CryptoKey} The key to export
* @returns {Promise<string>}

**Example:**
~~~javascript
    crypto.generateKey({
        name: "RSA",
        moduleLength: 2048,
    }).then(function (keypair) {
        crypto.exportKey("PEM", keypair.publicKey).then(console.log)
        crypto.exportKey("PEM", keypair.privateKey).then(console.log)
    })
~~~



## crypto.generateX509Certificate(publicKey, authorityPrivateKey, authorityCertificate, body)
Generates a X509 Certificate with the given body and public key, signed by the given authority.

* @param {CryptoKey} publicKey The public key that will be signed
* @param {CryptoKey} authorityPrivateKey The private key used to sign the certificate
* @param {CryptoKey} authorityCertificate The certificate authority used to derive the new body. If set to null, the certificate is self-signed.
* @param {certificateBody} body The new certificate contents
* @returns {Promise<CryptoKey>} The signed certificate

**Example:**
~~~javascript
    /**
     * Fields that are allowed to be set when creating an x509 certificate.
     * @typedef {Object} certificateBody
     * @property {pkixName} [subject]
     * @property {number} [startDateUnix] The start date of the certificate in unix seconds
     * @property {number} [expiryDateUnix] The expiry date of the certificate in unix seconds
     */

    /**
     * Copied directly into pkix.Name when generating the certificate on the backend.
     * https://pkg.go.dev/crypto/x509/pkix#Name
     * 
     * @typedef {Object} pkixName
     * @property {string[]} Country
     * @property {string[]} Organization
     * @property {string[]} OrganizationalUnit
     * @property {string} Locality
     * @property {string[]} Province
     * @property {string[]} StreetAddress
     * @property {string[]} PostalCode
     * @property {string} SerialNumber
     * @property {string} CommonName
     */

    /**
     * Creates a self signed certificate
     */
    crypto.generateKey({
		name: "RSA",
		modulusLength: 4096,
	}).then(function(keypair) {
		return crypto.generateX509Certificate(
			keypair.publicKey,
			keypair.privateKey,
			null,
			{ subject: { CommonName: "MY NAME" } }
		)
	}).then(function(cert) {
        // TODO: Do something with your cert
	}).then(function(pem) {
		resp.success(pem);
	})

    /**
     * Creates a certificate from some keypair and logs it out
     */
    var authorityPrivate = "YOUR PRIVATE KEY"
    var authorityCert = "YOUR CERT"

    crypto.importKey("PEM", authorityPrivate).then(function(caPrivateKey) {
        crypto.importKey("PEM", authorityCert).then(function(caCert) {
            crypto.generateKey({
                name: "RSA",
                modulusLength: 4096,
            }).then(function(myKeypair) {
                crypto.generateX509Certificate(
                    myKeypair.publicKey,
                    caPrivateKey,
                    caCert,
                    { subject: { CommonName: "DEVICE NAME" } }
                ).then(function (cert) {
                    // Log out the cert and its private key
                    crypto.exportKey("PEM", cert).then(console.log)
                    crypto.exportKey("PEM", myKepair.privateKey).then(console.log)
                })
            })
        })
    })

~~~

## crypto.PlatformMtlsCertificate
Special constant that represents the platform's root CA certificate for MQTT mtls.
Can be used to create certificates to authenticate with the broker.
If there is not certificate on the platform for mTLS, using this will throw an error.

## crypto.PlatformMtlsPrivateKey
Special constant that represents the platform's root CA private key for MQTT mTLS. 
Can be used to create certificates to authenticate with the broker.
If the PEM file for mTLS on the platform does not contain a PRIVATE KEY block, using this will throw an error. 

**Example:**
~~~javascript
    /**
     * Generate a certificate that can be used for mTLS.
     */
    crypto.generateKey({
		name: "RSA",
		modulusLength: 4096,
	}).then(function(keypair) {
		return crypto.generateX509Certificate(
			keypair.publicKey,
			crypto.PlatformMtlsPrivateKey,
			crypto.PlatformMtlsCertificate,

            // CommonName must be the name of the device that will use this cert.
			{ subject: { CommonName: "DEVICE NAME" } }
		)
	}).then(function(cert) {
        // Log the cert
		crypto.exportKey("PEM", cert).then(console.log);
    })
~~~