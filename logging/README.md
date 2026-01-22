# OpenShift Observability - Logging

# Install OpenShift Lokistack Operator

- Create the openshift-operators-redhat namespace
```bash
oc apply -f lokistack/namespace.yaml
```

- First install the Lokistack operator
```bash
oc apply -f lokistack/operator-group.yaml
```

- Apply the subscription
```bash
oc apply -f lokistack/subscription.yaml

- Create Object Bucket Claim
```bash
oc apply -f lokistack/object-bucket-claim.yaml

- Create Lokistack cluster
```bash
oc apply -f lokistack/lokistack.yaml

# Install Red Hat OpenShift Logging Operator

- Create the logging namespace using `namespace.yaml`

```bash
oc apply -f logging-operator/namespace.yaml
```

- Create an `OperatorGroup`
```bash
oc apply -f logging-operator/operatorgroup.yaml
```

- Create a Subscription object to subscribe the namespace to the Red Hat OpenShift Logging Operator
```bash
oc apply -f logging-operator/subscription.yaml
oc get csv -n openshift-logging
```

- Create the required service account and permissions
```bash
oc create sa logging-collector -n openshift-logging
oc adm policy add-cluster-role-to-user logging-collector-logs-writer -z logging-collector -n openshift-logging
oc adm policy add-cluster-role-to-user collect-application-logs -z logging-collector -n openshift-logging
oc adm policy add-cluster-role-to-user collect-infrastructure-logs -z logging-collector -n openshift-logging
```

- Create the cluster log forwarder object
```bash
oc apply -f logging-operator/cluster-log-forwarder.yaml
```

## Create application and visualize logs

- Create the application namespace

```bash
oc apply -f namespace.yaml
```

- Switch to your project and make it the default in your configuration.

```bash
oc project logging-web-app
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

- Perform a couple of HTTP requests to generate some data

```bash
for i in {1..10}; do  curl -s $(oc get route logging-web-app -o jsonpath='{.spec.host}'); done
```


- Try to visualize data from Logging UI plugin in the OpenShift Console





