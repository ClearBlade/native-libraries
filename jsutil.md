The JSUtil library is a utility library for converting javascript objects to strings and formatting numbers and JSON objects for print output.

## util.inspect(var)

Returns the variable converted to a string.

~~~javascript
//perform a GET request
log("The response from the server was: "+util.inspect(myVar));
~~~

## util.format([args])

Returns a formatted string using a variable array

%s - String.
%d - Number (both integer and float).
%j - JSON.

~~~javascript
//perform a GET request
util.format("Hello %s, welcome to %s", "Steve", "Austin");
~~~ 
