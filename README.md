# native-libraries
Documentation for ClearBlade Native Code Libraries
These are ClearBlade's Native Libraries that are out-of-box. They can be used in Code Services.

### included by default

```javascript
/*
 * Meta information about your currently running code service.
 * @prop {string} system_key
 * @prop {string} system_secret
 * @prop {string} service_name
 * @prop {string} service_id
 * @prop {string} platform_url
 * @prop {string} node_id
 * @prop {boolean} is_edge
 * @prop {Object} code_versions - map of service/library name to the running version number
 * @prop {string} [edge_url] only defined when running on the edge
 * @prop {string} [edge_id] only defined when running on the edge
 */
var cbmeta;
```
