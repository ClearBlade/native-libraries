# Metrics

The metrics library provides an interface for creating and updating custom Prometheus metrics from within code services. Metrics are exposed on the platform's Prometheus endpoint and can be used to monitor and alert on application behavior. Metrics can only be used in long-running services and will be reset if the service is updated.

Each metric is automatically tagged with `system_key`, `service_name`, and `instance_id` labels. 

Note: Custom metrics are only available to developers and require Prometheus to be enabled in the platform configuration.

# Usage

1. __[Gauge](#gauge)__
2. __[Counter](#counter)__
3. __[Histogram](#histogram)__


---

# Gauge

A Gauge is a metric that represents a single numerical value that can arbitrarily increase or decrease. Use a Gauge for things like queue depths, in-progress request counts, or temperature readings.

## Gauge constructor

```javascript
/**
 * @param {string} name - The name of the gauge. Must be unique across all metrics on the platform. If a CustomMetricPrefix is configured on the platform, it will be prepended automatically.
 */
var gauge = new Gauge(name);
```

### Example

```javascript
var gauge = new Gauge("active_connections");
```

## gauge.add

Increases the gauge by the given value.

```javascript
/**
 * @param {number} value - The amount to add to the gauge. 
 * @param {Object} [labels] - A map of label key-value pairs to attach to this observation.
 * @returns {Promise}
 */
gauge.add(value, labels)
```

### Example

```javascript
var gauge = new Gauge("active_connections");
gauge.add(1, {region: "us-east"}).then(function() {
    resp.success("gauge updated");
}).catch(function(err) {
    resp.error("failed to update gauge: " + err);
});
```

## gauge.sub

Decreases the gauge by the given value.

```javascript
/**
 * @param {number} value - The amount to subtract from the gauge.
 * @param {Object} [labels] - A map of label key-value pairs to attach to this observation.
 * @returns {Promise}
 */
gauge.sub(value, labels)
```

### Example

```javascript
var gauge = new Gauge("active_connections");
gauge.sub(1, {region: "us-east"}).then(function() {
    resp.success("gauge decremented");
}).catch(function(err) {
    resp.error("failed to decrement gauge: " + err);
});
```

---

# Counter

A Counter is a cumulative metric that can only increase. Use a Counter for things like total requests served, errors encountered, or messages processed. 

## Counter constructor

```javascript
/**
 * @param {string} name - The name of the counter. Must be unique across all metrics on the platform. If a CustomMetricPrefix is configured on the platform, it will be prepended automatically.
 */
var counter = new Counter(name);
```

### Example

```javascript
var counter = new Counter("requests_total");
```

## counter.inc

Increments the counter by the given value. The value must be positive.

```javascript
/**
 * @param {number} [value=1] - The amount to add to the counter. Must be a positive number.
 * @param {Object} [labels] - A map of label key-value pairs to attach to this observation.
 * @returns {Promise}
 */
counter.inc(value, labels)
```

### Example

```javascript
var counter = new Counter("requests_total");
counter.inc(1, {method: "GET", status: "200"}).then(function() {
    resp.success("counter incremented");
}).catch(function(err) {
    resp.error("failed to increment counter: " + err);
});
```

---

# Histogram

A Histogram samples observations and counts them in configurable buckets. Use a Histogram for things like request durations or response payload sizes where you want to compute quantiles.

## Histogram constructor

```javascript
/**
 * @param {string} name - The name of the histogram. Must be unique across all metrics on the platform. If a CustomMetricPrefix is configured on the platform, it will be prepended automatically.
 * @param {number[]} [buckets] - An array of upper-bound values defining the histogram buckets. Defaults to the Prometheus default buckets: [0.005, 0.01, 0.025, 0.05, 0.1, 0.25, 0.5, 1, 2.5, 5, 10]. The bucket configuration is fixed on first use and cannot be changed until the service is restarted.
 */
var histogram = new Histogram(name, buckets);
```

### Example

```javascript
var histogram = new Histogram("request_duration_seconds", [0.1, 0.5, 1.0, 2.5, 5.0, 10.0]);
```

## histogram.observe

Records a single observation.

```javascript
/**
 * @param {number} value - The value to observe.
 * @param {Object} [labels] - A map of label key-value pairs to attach to this observation.
 * @returns {Promise}
 */
histogram.observe(value, labels)
```

### Example

```javascript
var histogram = new Histogram("request_duration_seconds", [0.1, 0.5, 1.0, 2.5, 5.0]);
var start = Date.now();

// ... do some work ...

var duration = (Date.now() - start) / 1000;
histogram.observe(duration, {handler: "processData"}).then(function() {
    resp.success("duration recorded");
}).catch(function(err) {
    resp.error("failed to record duration: " + err);
});
```

---

# Label guidelines

- Use the same set of label keys on every call to a given metric. Mixing different label key sets on the same metric will cause an error.
- Keep label cardinality low. Each unique combination of label values creates a new time series. Avoid using high-cardinality values such as user IDs or request IDs as label values.
- Unused label combinations are automatically pruned after the interval configured by `Debug.PrometheusLabelExpirationMinutes` in the platform debug settings.
