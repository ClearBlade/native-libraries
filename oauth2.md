This library is used to connect to different OAuth 2 servers and get an authorization token back. Google OAuth is the only OAuth 2 provider supported, at the moment.  

# Class: OAuth2()  

## Initialize  

This method creates an oauth2 object instance  
**Example**  
~~~javascript
	var oauth2 = new OAuth2();
~~~

## oauth2.googleAuth  
Connects to the Google OAuth endpoint and retrieves an authorization token 

#### Parameters ####  
* **options:**  is a json object with the following keys (email, authScope, privateKey)  

**Example:**
~~~javascript  
	var oauth2 = new OAuth2();
	var options = {
		email: "your_google_service_account",
		authScope: "https://www.googleapis.com/auth/bigquery https://www.googleapis.com/auth/compute", // Auth scopes seperated by spaces
		privateKey: "-----BEGIN PRIVATE KEY-----\nyour_private_key\n-----END PRIVATE KEY-----\n"
	};
	var callback = function(err, data) {
		if(err) {
			resp.error("Error: " + data);
		} else {
			resp.success("Success " + data);
		}
	};
	oauth2.googleAuth(options, callback);  
~~~  
