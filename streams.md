# Streams API
The Streams API allows code services to effeciently access, process, and pipe large amounts of data.

The following libraries include functions to access data as streams:
 - clearblade_async
 - fetch
 - mqtt

This document will cover some examples and tips for using streams in code services. Full documentation on the Streams API can be found [here](https://developer.mozilla.org/en-US/docs/Web/API/Streams_API).

## Examples 
### Pipe To
Code services which aim to pipe data from a source to a sink may realize large performance benefits by using the [pipeTo](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream/pipeTo) function. When a `ReadableStream` is piped directly to a `WritableStream`, the data never needs to be pushed into the JavaScript environment, leading to decreased memory usage and faster execution times. 

If piping to `resp.writable`, the response header and status may be set. This must be done before the first chunk is written to `resp.writable`.

```js
async function FetchStream(req, resp) {
  const fetchResponse = await fetch("https://google.com")
  resp.status(418)
  fetchResponse.stream().pipeTo(resp.writable)
}
```

### Transform Streams
Transform streams are a great tool for mutating large data streams while minimizing memory usage. However, they should be used with caution. All data traveling through a transform stream must be loaded into the engine. Additionally, data enqueued by the transform stream is not buffered before passing it to the `WritableStream` at the end of the pipeline. This means that if the transformer enqueues very small chunks of data, performance may suffer.
```js
async function StreamTransformer(req, resp) {
    // Make a file that contains numbers separated by newlines
    const files = ClearBladeAsync.FS("MyFiles")
    await files.writeFile("sandbox/data.txt", "1\n2\n3\n")

    // Open read stream to file
    const readStream = files.createReadStream("sandbox/data.txt")

    var decoder = new TextDecoder();
    var encoder = new TextEncoder();
    var buffer = "";

    // Create transform stream that adds one to each number
    const transformStream = new TransformStream({
        transform: function(chunk, controller) {
            buffer += decoder.decode(chunk, { stream: true });
            var lines = buffer.split("\n");
            buffer = lines.pop();

            for (var i = 0; i < lines.length; i++) {
                var line = lines[i].trim();
                if (line === "") continue;
                
                var num = parseInt(line, 10);
                var transformed = (num + 1).toString() + "\n";
                controller.enqueue(encoder.encode(transformed));
            }
        },
    });


    readStream.pipeThrough(transformStream).pipeTo(resp.writable)
}
```
