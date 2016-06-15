devopsdays

- ICE: inclusivity, complexity, empathy
- sidney dekker, john allspaw

# anomaly detection:
- static & dynamic properties
- nearest neighbor detection
- successfully auth'd credit card purchases
  - bands of normalcy
  - diurnal patterns

## data:
- time-stamped observations
- static attributes (context)
  - units (bytes, requests)
  - data type (counters, gauges
- dynamic attributes
  - cost
  - config
  - elastic services (load balancing/number of servers)
- relationships
  - containment (members of an LB cluster)
  - members in a workflow
    - ex: services hit in a request flow

## types
- point anomaly (uni-variate)
  - observation that's unusual when compared to all other values
  - ex: an outlier in a time series
  - not always very easy to detect: most agents report cumulative data, not interval data
- contextual anomaly (uni-variate + contextual)
  - unusual in a certain context, but not all
    - ex: seasonal variation; temperature anomaly across the day (you'd expect sudden drops!)
  - ex: 40º not anamolous at night, but weird to see during the day
- collective anomaly (multi-variate + contextual)
  - given a model that most of the data fits, and a confidence interval you'd expect values to fall into, you can detect anomalies that fall outside them.

## techniques:
- deterministic anomaly detection techniques
  - current state of the world
  - eyeballing dashboards
    - human eye's really good at detecting issues.
    - don't really scale. SO MANY GRAPHS. As the number of metrics you want to graph increases, the likelihood that you'll miss a metric increases, too.
  - static thresholds
    - hard to set these, especially if you're setting these manually.
    - sudden changes are really hard to detect -- you want to measure the derivative of the graph sometimes -- you don't care what the actual level is -- you care about a sudden change.
  - monotonic metrics
    - thresholds don't work if you have a cumulative metric.
  - transformation (uni-variate)
    - delt, rate, scale, min, max, rhmax, etc.
    - frequency with thresholds can be useful, but it doesn't work well for contextual anomalies (ie, the 40º day)

- multivariate
  - pearson product moment coefficient
    - the correlation between 2 variables
    - if you know 2 metrics are generally correlated, you can measure the difference between metrics.
    - ex: if you know revenue & requests/sec are correlated, you can detect an anomalous request pattern if the correlation drops
    - not great for when the 2 variables drop in concert

### statistical machine learning
- phase 1: learning
  - throw test metrics into an anomaly detector, and get parameters out the other end
- phase 2: detection
  - throw raw metrics & parameters into the anomaly detector, and get anomalies & corrective analysis, so you can change the parameters to reflect the new normal
ex:
- set up bands of normalcy
  - first band: univariate metric. Given this variable, what's the standard deviation you think this will have?
  - second band: multivariate model. use information from correlation with n other metrics, and determine how that might predict your metrics.


# being the first employee at a company

- going from proof of concept to a minimum viable product
- need some infrastructure running
- FFPR loop
  - fiddle, fail, process, rebuild
- knowledge
- application
- synthesis
- evaluation

### ASS (AWS instance, screen, service)
- scp --> ssh --> screen --> ^C --> enter

- criteria/report card for success
  - easy to onboard new engineers, continuous delivery with transactional updates, automated resource management, write as little code as possible
  - fail-fast: if something's spiraling out of control, just cut it off and kill it.

- green fielding new technologies
  - USE ALL THE SHINIES
  - get kubernetes running!
    - if you don't know what you're doing, Kubernetes is overwhelming. :(

- out-of-the-box coreOS components
  - etcd, fleet/kubernetes/ECS, systemd, docker running services
  - deploys: restart a service, it'll download new images from docker, etc.

- repeatable/automated builds

- ignorance begets frustration
  - "what crazy people wrote this software? I can't even get it started"
  - enter etcd
    - "new technology means better, right?"
  - coreOS is a linux container hypervisor -- not quite ubuntu.
    - a very very minimal OS

- just buy everything oh god.

- don't copy-paste your infrastructure.

