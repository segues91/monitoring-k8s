
# Monitoring stack based on Prometheus

Monitoring tools for Kubernetes use case based on Prometheus metrics. There are different services that can generate metrics, they work as exporters, such as Node exporter, Kube state metrics, and another Kubernetes components. Prometheus generates also his own metrics. All these values are stored in Prometheus timeseries database. When Grafana adds Prometheus datasource, it can expose metrics to visualize them in a dashboard.

All the files are stored in folders, depending on the service deployment. To deploy them, you have to execute:

```bash
kubectl create -f service-folder/.
```

Now, we will talk a little bit more about the different services in this stack. We will explain them sorted by first to last deployment.

## Kubernetes dashboard

Dashboard is a web-based Kubernetes user interface. You can use Dashboard to deploy containerized applications to a Kubernetes cluster, troubleshoot your containerized application, and manage the cluster resources. You can use Dashboard to get an overview of applications running on your cluster, as well as for creating or modifying individual Kubernetes resources (such as Deployments, Jobs, DaemonSets, etc). For example, you can scale a Deployment, initiate a rolling update, restart a pod or deploy new applications using a deploy wizard.

Dashboard also provides information on the state of Kubernetes resources in your cluster and on any errors that may have occurred.

After the yaml files are deployed, you have to set this steps:
* Execute:
  ```bash
  kubectl proxy $
  ```
* Access the dashboard:
  http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
* Authenticate using token: Execute bash script get-token.sh allocated in k8s-dashboard folder. Copy the result and paste to authentication page.

This service is deployed in kubernetes-dashboard namespace.

## Node exporter

Service that can export metrics from worker nodes in a Kubernetes cluster. Node exporter can export metrics like:
* CPU
* Disk stats
* Filesystem
* Memory
* Network
* Time

This service is deployed as daemonset. A DaemonSet ensures that all (or some) Nodes run a copy of a Pod. As nodes are added to the cluster, Pods are added to them. As nodes are removed from the cluster, those Pods are garbage collected. Deleting a DaemonSet will clean up the Pods it created.

This service is deployed in monitoring namespace.

## Kube-state-metrics

kube-state-metrics is a simple service that listens to the Kubernetes API server and generates metrics about the state of the objects. It is not focused on the health of the individual Kubernetes components, but rather on the health of the various objects inside, such as deployments, nodes and pods.

The metrics are exported on the HTTP endpoint /metrics on the listening port (default 8080).

This service is deployed in kube-system namespace.

## Alert manager

The Alertmanager handles alerts sent by client applications such as the Prometheus server. It takes care of deduplicating, grouping, and routing them to the correct receiver integrations such as email, PagerDuty, or Slack. It also takes care of silencing and inhibition of alerts.

In our case, alert manager will send alerts to Slack channel.

This service is exposed internally by 9093 port.

This service is deployed in monitoring namespace.

## Prometheus

Prometheus is a systems and service monitoring system. It collects metrics from configured targets at given intervals, evaluates rule expressions, displays the results, and can trigger alerts when specified conditions are observed.

In this case, this service is deployed exposing 9090 port externally to be able to access the Web UI. Node exporter, kube-state-metrics and Alert manager communicates with it to store all the metrics and send alerts if necessary.

Prometheus has a timeseries database that stores all the metrics, with a 15 days of retention. The query language to get data is PromQL.

This service is deployed in monitoring namespace.

## Grafana

Grafana allows you to query, visualize and alerts the metrics stored in a timeseries database. Our instance is deployed with 3000 port exposed externally.

We have added Prometheus as datasource in Grafana to access its metrics.

This service is deployed in monitoring namespace.