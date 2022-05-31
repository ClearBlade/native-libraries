This library allows users to run ONNX (https://onnx.ai) based AI models on the ClearBlade Platform and Edge
## ClearBladeAI.Model(options)

The model represents an ONNX AI Model. An options object is needed to initialize the Model. The options object contains the details needed to load the model in memory. Model files are stored on the platform/edge using the File Management system. More details on how to use the File Management system can be found on https://docs.clearblade.com/v/4/files/

* @param {object} options
* @param {string} options.bucket_set - Name of the bucket set containing the model file
* @param {string} options.path - Location of the ONNX model file in the bucket set
* @constructor
* @prop {Array} inputs - a list of inputs for the given model
* @prop {Array} outputs - a list of outputs for the given model

Example

~~~javascript
var options = {
  "bucket_set": "myAIDeployment",
  "path": "sandbox/predict_power_failure.onnx",
};

var model = new ClearBladeAI.Model(options);
~~~

## Model.exec(desiredOutputs, inputs)

This function supplies the data as inputs to the model and returns the output.

* @param {Array} desiredOutputs - a list of output names whose data you want returned in the output. An empty array will return all outputs.
* @param {Object} inputs - A map of input name to multi-dimensional array
* @param {Promise} - Returns a promise with an array containing the results

Example

~~~ javascript
var data = [[-0.455587488, -0.128916238, -0.218322404, 0.0420372568, 1.29698479, 0., 0., 0., 0., 1., 0., 0., 0., 0.], [-0.455587488, -0.128916238, -0.218322404, 0.0420372568, 1.29698479, 0., 0., 0., 0., 1., 0., 0., 0., 0.]];

model.exec(["label", "probabilities"], {"input1": data}).then(function(results) {
  resp.success("Predictions are: " + results);
}).catch(function(reason){
  resp.error("Model exec failed: " + reason.message);
});
~~~

## Data Preprocessing and Analysis

The ClearBlade Platform and Edge provides native support for data analysis and processing libraries like ```zebras.js```, ```danfo.js``` and ```scikit_learn.js```. Zebra APIs can be accessed via the ```Zebras``` object, Danfo APIs can be accessed via the ```Danfo``` object and Scikit Learn APIs can be accessed via the ```Scikit``` object. More information on their API usage can be found at:
- Zebras: https://zebrasjs.com
- Danfo: https://danfo.jsdata.org
- Scikit Learn: https://www.npmjs.com/package/scikit-learn 
