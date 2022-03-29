This library allows users to run ONNX (https://onnx.ai) based AI models on the ClearBlade Platform and Edge
## ClearBladeAI.Model(options)

The model represents an ONNX AI Model. An options object is needed to initialize the Model. The options object contains the details needed to load the model in memory. Model files are stored on the platform/edge using the File Management system. More details on how to use the File Management system can be found on https://docs.clearblade.com/v/4/files/

* @param {object} options
* @param {string} options.deployment - Name of the deployment containing the File Management system information
* @param {string} options.name - Name of the ONNX model
* @param {string} options.path - Location of the ONNX model file
* @param {string} options.input - The input number at which the input data will be supplied to the model (typically this is 0)
* @constructor

Example

~~~javascript
var options = {
  "deployment": "myAIDeployment",
  "name": "PredictPowerFailure",
  "path": "sandbox/predict_power_failure.onnx",
  "input": 0
};

var model = new ClearBladeAI.Model(options);
~~~

## Model.exec(data)

This function supplies the data as an input to the model on the specified input position (defined previously in the options object) and returns the output.

* @param {Array} data - A multi-dimensional array as an input
* @param {Promise} - Returns a promise with an array containing the results

Example

~~~ javascript
var data = [[-0.455587488, -0.128916238, -0.218322404, 0.0420372568, 1.29698479, 0., 0., 0., 0., 1., 0., 0., 0., 0.], [-0.455587488, -0.128916238, -0.218322404, 0.0420372568, 1.29698479, 0., 0., 0., 0., 1., 0., 0., 0., 0.]];

model.exec(data).then(function(results) {
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
