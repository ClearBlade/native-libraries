# Fetch

> “Gretchen, stop trying to make fetch happen, it’s not going to happen.” - Mean Girls (2004)

The fetch library allows you to perform asynchronous HTTP requests. It is similar to the browser's built-in [fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API). This library is included by default in all ClearBlade services.

Differences between ClearBlade fetch and browser fetch:
- `Response.text()` and `Response.json()` return values directly, instead of in a promise.
- `Response.body`, `Response.text()`, and `Response.json()` may be accessed multiple times.

## Reference

~~~javascript
/**
 * @typedef {Object} fetchOptions
 * @property {string} method - HTTP method (GET, POST, PUT, etc)
 * @property {string|Uint8Array} body - body data for POST/PUT calls
 * @property {Object|Map|Array} headers - custom headers to include with request
 * @property {AbortSignal} signal - abort signal to cancel fetch operation
 */
 
/**
 * Executes an HTTP call.
 * Promise resolves with a Response object when HTTP call returns.
 * @param {string} url - an absolute url (like https://www.google.com/my/path?query=some-query)
 * @param {fetchOptions} [options]
 * @returns {Promise<Response>}
 */
fetch(url[, options])
 
/**
 * @typedef {Object} Response
 * @property {Uint8Array} body - body data of HTTP response
 * @property {Headers} headers - response headers
 * @property {number} status - response status code (ex: 200)
 * @property {string} statusText - response status message (ex: '200 OK')
 * @property {string} url - full url the request was sent to
 * @property {boolean} ok - true if response code was 2XX
 */
 
/**
 * Decodes the response body as a UTF-8 string.
 * @returns {string} decoded body
 */
Response.text()

/**
 * Decodes the response body as JSON.
 * @returns {Object} decoded body
 */
Response.json()

/**
 * @typedef {Object} Headers
 * @property {Object} headers - header map in format map[string][]string
 */

/**
 * Returns the first value of the given header.
 * If the header you're looking for has multiple values, access the headers property directly.
 * @param {string} key - header (ex: 'Content-Type')
 * @returns {string} - header value (ex: 'application/json')
 */
Headers.get(key)

/**
 * Returns true if the headers object contains the given header.
 * @param {string} key - header
 * @returns {boolean}
 */
Headers.has(key)

/**
 * @typedef {Object} AbortSignal
 * @property {boolean} aborted - Whether or not the signal has been aborted
 * @property {string|undefined} reason - The reason for the abort. Undefined if not aborted.
 */

/**
 * If the signal has been aborted, throws an error with the abort reason
 */
AbortSignal.throwIfAborted()

/**
 * Returns an instance of an already aborted signal.
 * @static
 * @returns {AbortSignal}
 */
AbortSignal.abort()

/**
 * Returns an instance of a signal that will abort after a specified timeout.
 * @static
 * @param {number} timeoutMs The amout of time, in milliseconds, to wait until aborting.
 * @returns {AbortSignal}
 */
AbortSignal.timeout(timeoutMs)


/**
 * @typedef {Object} AbortController
 * @property {AbortSignal} signal - The underlying abort signal
 */

/**
 * Sends the abort on the underlying abort signal
 * @param {string} [reason] Optional reason for abort. Defaults to "AbortError".
 */
AbortController.abort(reason)
~~~

## Examples

~~~javascript
/**
 * getJSON fetches a json object using a GET request,
 * then returns the decoded object.
 * @return {Promise<Object>} decoded json response
 */
function getJSON() {
    return fetch('https://httpbin.org/json')
        .then(function(response) {
            if(!response.ok) throw new Error(response.statusText + ": "+response.text());
            return response.json();
        })
}

/**
 * getTextOrJSON fetches an HTTP response using a GET request.
 * If the response contains the 'Content-Type: application/json' header,
 * it decodes the response as JSON.
 * Otherwise, decodes the response as a UTF-8 encoded string.
 * @return {Promise<Object|string>} decoded HTTP response
 */
function getTextOrJSON() {
    return fetch('https://httpbin.org/encoding/utf8')
        .then(function(response){
            if(!response.ok) throw new Error(response.statusText + ": "+response.text());
            if(response.headers.get('Content-Type') === 'application/json') return response.json();
            return response.text();
        })
}

/**
 * postTextData sends a POST request with the provided bodyData.
 * It adds a custom header to the request with our secret key.
 * @param  {string} bodyData
 * @return {Promise<Object>} decoded json response
 */
function postTextData(bodyData) {
    var options = {
        method: 'POST',
        body: bodyData,
        headers: {
            'Content-Type': 'text/plain',
            'X-Secret-Key': 'mySuperSecretAPIKey'
        }
    }
    return fetch('https://httpbin.org/post', options)
        .then(function(response) {
            if(!response.ok) throw new Error(response.statusText + ": "+response.text());
            return response.json();
        })
}

/**
 * fetchWithTimeout sends a GET request with a timeout signal
 * @return {Promise<string>} Raw response
 */
function fetchWithTimeout(bodyData) {
    const url = "https://httpstat.us/200?sleep=2000";
    const signal = AbortSignal.timeout(3000);
    return fetch(url, { signal })
        .then(function (response) {
            if(!response.ok) throw new Error(response.statusText + ": "+response.text());
            return response.text();
        });
}
~~~