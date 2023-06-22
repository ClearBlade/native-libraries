This library allows users to run ONNX (https://onnx.ai) based AI models on the ClearBlade Platform and Edge.
## ClearBladeAI.Model(options)

An options object is needed to initialize the models. The options object contains the details needed to load the model in memory. The file management system stores model files on the Platform and Edge. More details on how to use the file management system can be found at https://docs.clearblade.com/v/4/files/.

* @param {object} options
* @param {string} options.bucket_set: The bucket set name containing the model file
* @param {string} options.path: Location of the ONNX model file in the bucket set
* @constructor
* @prop {Array} inputs: A list of inputs for the given model
* @prop {Array} outputs: A list of outputs for the given model

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

* @param {Array} desiredOutputs: A list of output names whose data you want returned in the output. An empty array will return all outputs.
* @param {Object} inputs: A map of input name to a multi-dimensional array.
* @param {Promise}: Returns a promise with an array containing the results.

Example

~~~ javascript
var data = [[-0.455587488, -0.128916238, -0.218322404, 0.0420372568, 1.29698479, 0., 0., 0., 0., 1., 0., 0., 0., 0.], [-0.455587488, -0.128916238, -0.218322404, 0.0420372568, 1.29698479, 0., 0., 0., 0., 1., 0., 0., 0., 0.]];

model.exec(["label", "probabilities"], {"input1": data}).then(function(results) {
  resp.success("Predictions are: " + results);
}).catch(function(reason){
  resp.error("Model exec failed: " + reason.message);
});
~~~

## Data preprocessing and analysis

The ClearBlade Platform and Edge provide native support for data analysis and processing libraries like ```zebras.js```, ```danfo.js```, and ```scikit_learn.js```. Zebra APIs can be accessed via the ```Zebras``` object. Danfo APIs can be accessed via the ```Danfo``` object and Scikit. Learn APIs can be accessed via the ```Scikit``` object. More information on their API usage can be found at:
- Zebras: https://zebrasjs.com
- Danfo: https://danfo.jsdata.org
- Scikit learn: https://www.npmjs.com/package/scikit-learn
