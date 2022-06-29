The Log library is a library for issuing log statements during the execution of a service.  These logs can be reviewed later from the console or the returning payload

## log(STRING)

Issue a statement to log a message to the service logs. Log-level for these log messages is set at "info".

~~~javascript
//perform a GET request
log("The response from the server was: "+JSON.stringify(myVar));
~~~

## console.log(STRING)
Issue a statement to log a message to the service logs. Log-level for these log messages is set at "info".

~~~javascript
//perform a GET request
console.log("The response from the server was: "+JSON.stringify(myVar));
~~~

## console.info(STRING)
Issue a statement to log a message to the service logs. Log-level for these log messages is set at "info".

~~~javascript
//perform a GET request
console.info("The response from the server was: "+JSON.stringify(myVar));
~~~

## console.debug(STRING)
Issue a statement to log a message to the service logs. Log-level for these log messages is set at "debug".

~~~javascript
//perform a GET request
console.debug("The response from the server was: "+JSON.stringify(myVar));
~~~

## console.warn(STRING)
Issue a statement to log a message to the service logs. Log-level for these log messages is set at "warn".

~~~javascript
//perform a GET request
console.warn("The response from the server was: "+JSON.stringify(myVar));
~~~

## console.error(STRING)
Issue a statement to log a message to the service logs. Log-level for these log messages is set at "error".

~~~javascript
//perform a GET request
console.error("The response from the server was: "+JSON.stringify(myVar));
~~~

## console.alert(STRING)
Issue a statement to log a message to the service logs. Log-level for these log messages is set at "alert".

~~~javascript
//perform a GET request
console.alert("The response from the server was: "+JSON.stringify(myVar));
~~~
