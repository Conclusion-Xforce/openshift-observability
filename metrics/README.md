# OpenShift Observability - Metrics


## Deploy  user-workload-monitoring

- Check whether the cluster-monitoring-config ConfigMap object exists:

```bash
oc -n openshift-monitoring get configmap cluster-monitoring-config -o yaml
```

- Review the `cluster-monitoring-config.yaml`
- Apply the configuration to enable the monitoring for user-defined projects in a cluster.
```
oc apply -f cluster-monitoring-config.yaml
```

- Review the `user-workload-monitoring-config.yaml`
- Apply the configuration to create the ConfigMap object that saves the prometheus data for 10days
```
oc apply -f user-workload-monitoring-config.yaml
```

- Check that the prometheus-operator, prometheus-user-workload and thanos-ruler-user-workload pods are running in the openshift-user-workload-monitoring project. It might take a short while for the pods to start
```bash
oc -n openshift-user-workload-monitoring get pod
```

## Metrics

- Review the files under the `metrics` folder

- Create the application namespace

```bash
oc apply -f namespace.yaml
```

- Switch to the prometheus-example-app project and make it the default in your configuration.

```bash
oc project prometheus-example-app
```

- Apply the configuration to the cluster

```bash
oc apply -f deployment.yaml
oc apply -f service.yaml
oc apply -f route.yaml
```

- Check that the pod is running

```bash
oc get pods
oc get all
```


To use the metrics exposed by your service, you must configure OpenShift Platform monitoring to scrape metrics from the `/metrics` endpoint. You can do this using a `ServiceMonitor` custom resource definition (CRD) that specifies how a service should be monitored, or a `PodMonitor` CRD that specifies how a pod should be monitored. The former requires a Service object, while the latter does not, allowing Prometheus to directly scrape metrics from the metrics endpoint exposed by a pod.

- Create a `service-monitor.yaml` file for a ServiceMonitor resource configuration. 

```yaml
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  labels:
    k8s-app: prometheus-example-monitor
  name: prometheus-example-monitor
spec:
  endpoints:
  - interval: 30s
    port: web
    scheme: http
  selector:
    matchLabels:
      app: prometheus-example-app
```

This defines a ServiceMonitor resource that scrapes the metrics exposed by the prometheus-example-app sample service.

- Apply the configuration to the cluster

```bash
oc apply -f service-monitor.yaml
```

> Note: It takes some time to deploy the ServiceMonitor resource.

- You can check that the ServiceMonitor resource is running

```bash
oc get servicemonitor
```

- Perform a couple of HTTP requests to generate some data

```bash
for i in {1..10}; do  curl $(oc get route prometheus-example-app -o jsonpath='{.spec.host}'); curl $(oc get route prometheus-example-app -o jsonpath='{.spec.host}')/404; done
```

Querying metrics
----------------

- From the Developer perspective in the OpenShift Container Platform web console, select Observe → Metrics.

- Select the project that you want to view metrics for in the Project: list.

- Select a query from the Select query list, or create a custom PromQL query based on the selected query by selecting Show PromQL. The metrics from the queries are visualized on the plot.

- Query one of the metrics exposed by the example app `http_requests_total`




END
--------------------


# Extra: Thanos

https://killercoda.com/thanos
