# OpenShift Observability - Alerts


## Alerts

- Switch to the prometheus-example-app project and make it the default in your configuration.

```bash
oc project prometheus-example-app
```

- Check that it is still running 
```bash
oc get pods
oc get all
```

- Now go to the Observe view of the project (in Developer view) to review the available dashboards.

- Now let's create a new Alert rule - This will create an alert if the number of running pods is less than 2

```bash
oc apply -f prometheusRule.yaml
```

- Now look at the Alert Rule tab (in Administrator view) you can see this newly created “user” alert rule.

- The condition is already triggered and the alert is "Firing".

- Go and increase the number of running PODs to 2, then the alert will stop firing and if you notice the Observe tab and look at the history for the last 5 minutes, you can see there is no current alert




## OpenShift Alertmanager Configuration

You will now configure alert manager in OpenShift to route alerts from a specific namespace to Slack. 

We will need to be logged on as cluster-admin to configure alert manager.

- Log on to Openshift console as cluster-admin.

- From the left menu, select Administration > Cluster settings.

- On the Cluster Settings page, select Configuration > Alertmanager.

- On the Alertmanager page, select Create Receiver.
    
- Enter a name for Receiver and select Slack as receiver type. 

- In the Slack API URL box, paste the Webhook URL from slack
`https://hooks.slack.com/services/<will-be-provided>`

- In the channel box, provide the slack channel name (without the #) to post alerts to `alerts`.

- In the router labels section, enter `namespace` for `Name` and `prometheus-example-app` for `Value`, to route alerts from the prometheus-example-app namespace to the slack workspace.

- Select save. We should start receiving firing alerts within our namespace in a few seconds. 

> Remember to scale down the `prometheus-example-app` to trigger the alert we fixed earlier.




END
--------------------


