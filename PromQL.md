# Monitoring on Kubernetes - Part 3

# Prom QL
This is the query language used in Prometheus.
### Data Types
- String -> text
- Scaler -> numeric
- Instant vector -> return 1 value for a specific period
- Range vector -> returns a range of values for a specific period

### Metrics
- Counter -> for values that increase with time, returns an instant vector
- Gauge -> for values that increase or decrease with time
- Histogram -> gives the output for predefined intervals of a specific period
- Summary -> gives the output for a specific period, but where the intervals are unknown

### Labels & Filters
- useful to filter out data and optimize queries

### Basic Guidelines
Reference: https://github.com/kubernetes/kube-state-metrics/blob/main/docs

- Metric Name{filters}[time] where filter denoted as key="value" 
	- metric name is similar to a simple select * from table.
	- filters are like the where condition.
	- time is the range in days, months, secs, etc.
- Additional functions like avg(), sum(), min(), max(), rate() are available.

### Examples
1. Display the absolute memory usage of a specific pod at the current time.
	
 ***container_memory_usage_bytes{namespace="monitoring", pod="prometheus-deployment-556fbcc476-7fvdj"}*** ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/9851f336-958b-4dc3-bc46-5066b266395e)
 
2. Display the absolute memory usage of a specific pod over the last 5 minutes.
	
 ***container_memory_usage_bytes{namespace="monitoring", pod="prometheus-deployment-556fbcc476-7fvdj"}[5m]*** ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/4ed56e08-8b53-4dd9-bfb4-5cd19743d253)
 
3. Display the rate of change of memory usage of a specific pod over the last 5 minutes.
	
 ***rate(container_memory_usage_bytes{namespace="monitoring", pod="prometheus-deployment-556fbcc476-7fvdj"}[5m])*** ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/3dd3e2e4-a9fd-4b74-b25e-2cf5f58b46a2)
 
4. Display absolute memory usage of pods, show only namespaces and pod names.
	
 ***sum by (namespace,pod)(container_memory_usage_bytes)*** ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/a55720b2-7eaa-4011-a0bb-4537121363af)
 
5. By default the output is in bytes, convert to MB and GB.
	
 ***MB :  sum by (namespace,pod)(container_memory_usage_bytes)/ (1024 * 1024 ) 
GB : sum by (namespace,pod)(container_memory_usage_bytes)/ (1024 * 1024 * 1024)***  ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/236395b0-a2b1-47a4-8322-698ff4c995e7)


