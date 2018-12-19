The HTTP library is a library for making HTTP calls to external services. It supports four HTTP verbs (GET,POST,DELETE,PUT).

# Class: Requests

The Requests class is invoked to get an HTTP requesting object. Note that it depends on the ClearBlade object, so the ClearBlade object must also be included in your service.

## HTTP Request Options
Options passed into each request

- **uri:** The url to be requested
- **qs:** Query string parameters
- **body:** Request Payload, in the form of a JSON Object. Not valid on verbs that do not have bodies (such as DELETE or GET).
- **form:** only applicable on the POST method.
- **headers:**  Header properties to be passed of the request
- **followRedirect:** a number, the maximum number of redirects to follow
- **timeout:** The amount of seconds to wait for a response
- **auth:** Support for HTTP basic auth. the object must have the "user" and "pass" keys. all others will be ignored.
- **strictSSL:**  Fails the request if the identity on the SSL certificate cannot be verified. set to false to use with self-signed certs
- **getProxyUrl:** If you are using a proxy to call the outside world, and need to get the proxy URL from an environment variable, set this to true
- **isSoap:** Force an XML based response  
- **full:** Gets back all the HTTP response headers



## request.get


### Simple HTTP get Example
~~~javascript  
	//get a Requests object
	var requestObject = Requests();
    var options = {
        uri:"https://httpbin.org/get"
    }
    requestObject.get(options,function(err, data){
        if(err){
            resp.error("Unable to HTTP GET: " + JSON.stringify(err))
        }
        resp.success(data)
    })
}
~~~

### Advanced HTTP get Example

~~~javascript
//get a Requests object
var requestObject = Requests();

//options are fed into each object.
var options = {
    "uri":"https://google.com",
    "qs": {"q":"clearblade platform"},
    "body": {msg:"hello world"},
    "form":true,
    "headers": {"Content-Type" : "application/json","date":"Thu, 13 Aug 2015 21:24:53 GMT","Server":"nginx/1.2.1"},
    "followRedirect":12, 
    "timeout":30,
    "auth":{"user":"me","pass":"secret"},
    "strictSSL":false,
    "getProxyUrl":false,
    "isXML":true,
    "isSoap":true,
    "full": true  
}

requestObject.get(options,function(err, data){
    if(err){
        resp.error("Unable to HTTP GET: " + JSON.stringify(err))
    }
    resp.success(data)
})

~~~

## request.post
Execute POST on the http request object
~~~javascript
//performs a POST request
requestObject.post(options,function(err,body){
	if(err){
		//the error is a JSON value of the error in question, shaped like {"error":"message"}
	}else{
		//body is JSON of the response
	}
})
~~~

## request.delete
Execute DELETE on the request object
~~~javascript
//performs a delete
requestObject.delete(options,function(err,body){
	if(err){
		//the error is a JSON value of the error in question, shaped like {"error":"message"}
	}else{
		//body is JSON of the response
	}
})
~~~

## request.put
Execute PUT on the request object
~~~javascript
//performs a put
requestObject.put(options,function(err,body){
	if(err){
		//the error is a JSON value of the error in question, shaped like {"error":"message"}
	}else{
		//body is JSON of the response
	}
})
~~~
