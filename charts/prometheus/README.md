# Setting up Prometheus on kubernetes  

>*[tutorial on LinuxAcademy](https://linuxacademy.com/blog/kubernetes/running-prometheus-on-kubernetes/)* 
This tutorial assumes that you already have a working kubernetes cluster, 
and that your respective ports are available `9090-9094`  

---

**Create** the `namespace.yml` and deploy it with  
`kubectl apply -f namespace.yml`  

**Create** the `clusterRole.yml` and deploy with  
`kubectl apply -f clusterRole.yml`  

**Create** `config-map.yml`  
This is creating the Prometheus targets using service discovery with the Kubernetes API. This is the reason why we needed to configure the cluster roles earlier. Without it, Prometheus wouldn’t have the necessary permissions to access the APIs to discover the targets.  

The following jobs are being configured as targets using service discovery:  
  * `kubernetes-apiservers`: Gets metrics on the Kubernetes APIs.  
  * `kubernetes-nodes`: Gets metrics on the Kubernetes nodes.  
  * `kubernetes-pods`: Gets metrics from Pods that have the prometheus.io/scrape and prometheus.io/port annotations defined in the metadata.  
  * `kubernetes-cadvisor`: Gets cAdvisor metrics reported from the Kubernetes cluster.  
  * `kubernetes-service-endpoints`: Gets metrics from Services that have the prometheus.io/scrape and prometheus.io/port annotations defined in the metadata.  

> By using service discovery, we don’t need to update the prometheus.conf file with new pods and services as they come online and offline. As long as the prometheus.io/scrape and prometheus.io/port annotations are defined in the metadata of your pods and services, Prometheus will automatically be updated with the targets.  

and deploy with  
`kubectl apply -f config-map.yml`  

**Create** the `prometheus-deployment.yml`  
To make the service available, set `prometheus.io/scrape` to `true`. Then, you need to make sure that `prometheus.io/port` is the targetPort defined in the service. If you don’t, the target will not be discovered.  

Then apply it with: `kubectl apply -f prometheus-deployment.yml`  

---

You can verify that this all worked with:  
`kubectl get pods -n monitoring`
`kubectl get services -n monitoring`

---

Once the pod and service are available, you can access Prometheus’s Expression Browser by going to -  
`http://<KUBERNETES_MASTER_IP>:9090`  

---

| Contact: |
| :---------: |
| **[Slack](https://101101workspace.slack.com/archives/D012ESWSXHQ "dsmith73 on 101101 workspace")**  / **[Discord](https://discord.gg/RmzVNzx)** |
| ![github.com/dsmith73](https://avatars1.githubusercontent.com/u/44279121?s=60&u=7a933a33b51505f9d6435eeffae1c8156a47dc77&v=4 "github.com/dsmith73") |
