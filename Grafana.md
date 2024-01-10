# Monitoring on Kubernetes - Part 5
# Grafana

- Grafana is used to visualize data from sources like Prometheus, AWS cloudwatch etc. 
- Setup Alert Manager for this series based on the [Tutorial](https://devopscube.com/setup-grafana-kubernetes/)

## Resources 
The Kubernetes resources that were created in the *monitoring namespace* to setup Grafana are as follows:
- Config-map: grafana-datasources(prometheus url)
- Deployment: grafana
- Service: grafana

## How it works
- In the config map, we specify the Prometheus service dns  "url: http://prometheus-service.monitoring.svc:8080"
- This config map is used via a volume mount in the deployment
- In the Grafana UI, while adding the Data Source we need to specify the Prometheus service name i.e. http://prometheus-service:8080 or dns http://prometheus-service.monitoring.svc:8080.

- Few dashboards I managed to import:
![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/fffb24ae-0e00-42ec-accc-63813490b78c)

![image](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/76284ca2-06b9-4424-8f9c-c426b15fd826)

