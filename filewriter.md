The file library helps to create files on server and remove when needed.

# Writer()

##  Initialization
This method creates an object for the file library.	

~~~javascript
	var fileWriter = new Writer();
~~~

## fileWriter.writeToFile
This method writes the specified data to a file in txt/doc format. On successful write, a url is returned which points to the location of file on server

* host - the server on which platform is running
* data - string that is to be written to file
* file_name - name of file as string

**Example:**
	
~~~javascript 
	var fileWriter = new Writer();
	var text = "This is a test string";
	var fileName = "Test.doc";
	fileWriter.writeToFile("sfopera.com", text, fileName, function(err,url) {
		if (err) {
			resp.error("Write failed");
		} else {
			resp.success(url);
		}
	});
~~~

## file_writer.removeFile
This method removes the file from server on which it is stored. Cannot be retrieved again.

* file_name - name of file to be removed as string					

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
