# Node.js compatibility

ClearBlade services include limited Node.js functionality. This document contains a list of the implemented classes, methods, and events. 

Like in Node.js, you can use `require("moduleName")` to include the specified module. However, you cannot use require to include other code files or libraries. 

See detailed documentation of the following at [the official node docs](https://nodejs.org/dist/latest-v16.x/docs/api/).

- Module: events
  - Class: events.EventEmitter
    - emitter.emit()
    - emitter.on()
- Module: net
  - Class: net.Socket
    - new net.Socket()
    - event 'close'
    - event 'connect'
    - event 'data'
    - event 'end'
    - socket.connect()
    - socket.end()
    - socket.setKeepAlive()
    - socket.write()
  - Class net.Server
    - new net.Server()
    - event 'close'
    - event 'connection'
    - event 'error'
    - event 'listening'
    - server.listen()
    - server.close()
  - net.connect()
  - net.createConnection()
  - net.createServer()
- Module: stream
  - Class: stream.Writable
    - writable.write()
    - event 'close'
    - writable.end()
    - writable.destroy()
  - Class: stream.Readable
    - event 'close'
    - event 'end'
    - event 'data'
    - readable.push()
    - readable.pipe()
    - readable.destroy()
  - Class: stream.Duplex
- Module: dgram
  - Class: dgram.Socket
    - event 'close'
    - event 'error'
    - event 'listening'
    - event 'message'
    - socket.address()
    - socket.bind()
    - socket.close()
  - dgram.createSocket()
- Module: child_process
  - child_process.execSync()
