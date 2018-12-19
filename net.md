The socket library is used for communicating with servers using raw TCP/UDP sockets.

**Methods**

* new net_cb.Socket()
* socket.connect()
* socket.readWithSize()
* socket.read()
* socket.write()
* socket.lookup()
* socket.bytesRead()
* socket.destroy()


# Class: net_cb.Socket()

## Initialization

This method creates an object needed to interact with the socket library
.
**Example**
~~~javascript
	var socket = new net_cb.Socket();
~~~

## socket.connect
Opens a new socket and connects to the specified server.

#### Parameters ####
* **connection_url:**  can be of the form (ip_address:port)
* **type:** is the type of socket (tcp", "tcp4" (IPv4-only), "tcp6" (IPv6-only), "udp", "udp4" (IPv4-only), "udp6" (IPv6-only), "ip", "ip4" (IPv4-only), "ip6" (IPv6-only), "unix", "unixgram" and "unixpacket")	

**Example:**
~~~javascript
		var callback = function (err) {
			if (err) {
	    		resp.error("Could not connect");
			} else {
	    		resp.success("Connected");
			}
    	};
		socket.connect("127.0.0.1:9999","tcp",callback);	
~~~
## socket.readWithSize		

Read a specified number of bytes from the server. This method blocks until the buffer is filled with the specified number of bytes and returns data to the user. 
	
If no data is received for 10 seconds, a timeout occurs and control is given back to the user.

#### Parameters ####

* **size:** is size in bytes

**Example:**
~~~javascript
	var callback = function(err,data) {
		if (err) {
			resp.error("error");
		} else {
			resp.success(data);
		}
	};
	socket.readWithSize(100,callback);	
~~~

## socket.read()

This function should be used to read a stream of bytes from a server. 

**Example:**
~~~javascript		
	var readCallback = function(data){
    	respData+=data;
		if(respData.search('\n')){
	            resp.success("success");
		}
	};
	socket.read(callback);
~~~
## socket.write
	
This function is used to send a request to a server. The response can be read using read() or readWithSize() methods.

#### Parameters ####
* **request** can be of any type that server can read (bytes,string,...)

**Example:**
~~~javascript
	var writeCallback = function(err,info){
		if(err){
		    resp.error({"failure":info});
		}else{
	        *** Ideally you would be calling read from here ***   
		}
	};
	socket.write("Hello I am a test\n",callback);	
~~~

## socket.lookup

This function is used to provide name resolution using the IP address.
#### Parameters
* ip - the IP address

**Example:**
~~~javascript
	socket.lookup("127.0.0.1",function(err,host){
		if (err){
			resp.error("error " + err);
		}else{
			resp.success(host);
		}
	});
~~~

## socket.bytesRead

This function returns the number of bytes read from the server. This can be used to verify that no data loss has occurred.

#### Parameters ####
* **message:** is the data that is returned from read() or readWithSize() methods.

**Example:**
~~~javascript
	socket.bytesRead("This is a message of random length",function(err,val){
		if (err){
			resp.error("error " + val);
		}else{
			resp.success(val);
		}
	});	
~~~

## socket.destroy
This function closes the connection to server.			

**Example:**
~~~javascript
	socketObject.destroy(function(err,succ){
		if (err){
			resp.error("error " + err);
		}else{
			resp.success(succ);
		}
	});
~~~
