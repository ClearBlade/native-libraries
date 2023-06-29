The file library helps to create files on the server and remove them when needed.

# Writer()

##  Initialization
This method creates an object for the file library.	

~~~javascript
	var fileWriter = new Writer();
~~~

## fileWriter.writeToFile
This method writes the specified data to a file in txt/doc format. On successful write, a URL is returned, which points to the file's location on the server.

* host: The server on which the Platform is running
* data: String that is to be written to a file
* file_name: File name as a string

**Example:**
	
~~~javascript 
	var fileWriter = new Writer();
	var text = "This is a test string";
	var fileName = "Test.doc";
	fileWriter.writeToFile("yourdomain.com", text, fileName, function(err,url) {
		if (err) {
			resp.error("Write failed");
		} else {
			resp.success(url);
		}
	});
~~~

## file_writer.removeFile
This method removes the file from the server on which it is stored. It cannot be retrieved again.

* file_name: File name to be removed as a string					

**Example:**

~~~javascript
	var fileWriter = new Writer();
	var fileName = "test_syskey/Test.doc";
	fileWriter.removeFile(fileName, function(err,url) {
		if (err) {
			resp.error("Write failed");
		} else {
			resp.success(url);
		}
	});	
~~~
