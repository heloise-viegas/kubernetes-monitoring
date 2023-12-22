# Monitoring on Kubernetes - Part 2
In part 1 we setup Prometheus on our Kubernetes cluster. Now we proceed to setup Kube State Metrics to get details of our Kubernetes resources. 
Here is the complete [Tutorial](https://devopscube.com/setup-kube-state-metrics/) that I have followed. 
P.S. These are my observations and key pointers while performing this setup. 

# Kube Metrics
- Kube Metrics is responsible for providing details from Kubernetes API regarding the various resources deployed on the cluster.
Issues on Minikube:
- [docker - kubectl top node `error: metrics not available yet` . Using metrics-server as Heapster Depricated - Stack Overflow](https://stackoverflow.com/questions/52694238/kubectl-top-node-error-metrics-not-available-yet-using-metrics-server-as-he)
- [kubernetes - Failed to install metrics-server on minikube - Stack Overflow](https://stackoverflow.com/questions/70740997/failed-to-install-metrics-server-on-minikube)
- [Easily Install Kubernetes Metrics Server in minikube Now! 🧊 (thospfuller.com)](https://thospfuller.com/2020/11/29/easy-kubernetes-metrics-server-install-in-minikube-in-five-steps/)
- [kubernetes - Why is my minikube metrics-server addon not reporting pod metrics (docker 20.x)? - Stack Overflow](https://stackoverflow.com/questions/72139279/why-is-my-minikube-metrics-server-addon-not-reporting-pod-metrics-docker-20-x)
- [kubernetes - Failed to install metrics-server on minikube - Stack Overflow](https://stackoverflow.com/questions/70740997/failed-to-install-metrics-server-on-minikube)

## Resources 
The Kubernetes resources that were created to setup Prometheus are as follows:
- Cluster Role: kube-state-metrics
- Cluster Role Binding: kube-state-metrics
- Service Account: kube-state-metrics
- Deployment: kube-state-metrics
- Service: kube-state-metrics
All resources are deployed in kube-system namespace. For more details on these resources refer to Part1 of this series.


- For Kube-state-metrics state to be Up in Prometheus dashboard the following is configured: the config map in Part1 containing the Prometheus scrape config i.e. **prometheus-server-conf** job i.e. **kube-state-metrics** has the DNS of the kube-state-metrics service

      - job_name: 'kube-state-metrics'
        static_configs:
         - targets: ['kube-state-metrics.kube-system.svc.cluster.local:8080']

- Once we deploy the Kube-state-metrics as shown in the tutorial and verify that the above code is present in the config map we should see the kube-state- 
  metrics with status UP.![MicrosoftTeams-image (1)](https://github.com/heloise-viegas/kubernetes-monitoring/assets/37453877/1421d407-32c2-4583-9614-ef0a29701128)

