The Log library is a library for issueing log statements during the execution of a service.  These logs can be reviewed later from the console or the returning payload

## log(STRING)

Issue a statement to log a message to the service logs.

~~~javascript
//perform a GET request
log("The response from the server was: "+JSON.stringify(myVar));
~~~
