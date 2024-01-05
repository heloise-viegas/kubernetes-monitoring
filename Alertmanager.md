# Monitoring on Kubernetes - Part 4
# Alerting

- Alert manager is used with Prometheus to manage alerts. 
- It can be integrated with various tools like Slack, Email etc.
- Setup Alert Manager for this series based on the [Tutorial](https://devopscube.com/alert-manager-kubernetes-guide/)


## Resources 
The Kubernetes resources that were created in the *monitoring namespace* to setup Alert Manager are as follows:
- Config-maps: alertmanager-config(path to templates, interval, email-id etc), alertmanager-templates(email templates etc)
- Deployment: alertmanager
- Service: alertmanager

## How it works
- Once the Alert manager service is up, ensure that this endpoint is specified in the Prometheus Config map (prometheus-server-conf) under prometheus.yaml
```
prometheus.yml: |-
  global:
	scrape_interval: 5s
	evaluation_interval: 5s
  rule_files:
	- /etc/prometheus/prometheus.rules
  alerting:
    alertmanagers:
	- scheme: http
	  static_configs:
      - targets:
        - "alertmanager.monitoring.svc:9093"
```
- The actual alert rules are also specified in the Prometheus Config map (prometheus-server-conf) under prometheus.rules 
```
prometheus.rules: |-
  groups:
  - name: devopscube demo alert
    rules:
    - alert: High Pod Memory
      expr: sum(container_memory_usage_bytes) > 1
      for: 1m
      labels:
	     severity: slack
	  annotations:
		 summary: High Memory Usage
   - alert: Instance Down
      expr: 1
      for: 1m
      labels:
	     severity: slack
	  annotations:
		 summary: Instance Down
	- alert: Pod Crashed
      expr: min_over_time(sum by (namespace, pod) (kube_pod_status_phase{phase=~"Pending|Unknown|Failed"})[15m:1m]) > 0
      for: 1m
      labels:
	     severity: slack
	  annotations:
		 summary: Pod Crashed
		 
```
- Since the config map is mounted as a volume in the Prometheus deployment container, the config map keys are updated automatically without needing a pod restart
- Unfortunately, this didn't work on my minikube cluster and I had to restart the pod to get the updated config map
- Prometheus rules :
  ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/87e7ba48-bfe4-41bb-9875-d254f61e00bb)
- Alert Manager :
  ![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/f74a01fe-e55c-4b20-9aff-cde5e2d68b05)

