# Update and upgrade Splunk k8s Otel collector configuration

## Gather values

### Create the values.yaml file with custom configuration

##### splunk-otel-collector values can be specified with the 'set' argument or the values.yaml file.

Here is an example of using the set argument to specify values

```cmd
helm upgrade splunk-otel-collector --set="cloudProvider=azure,distribution=aks,splunkObservability.accessToken=$ACCESS_TOKEN,clusterName=my-kube-cluster,splunkObservability.realm=us0,gateway.enabled=false,splunkPlatform.endpoint=https://http-inputs-myorg.splunkcloud.com:443,splunkPlatform.token=$HEC_TOKEN,splunkObservability.profilingEnabled=true,environment=production,operator.enabled=true,certmanager.enabled=true,agent.discovery.enabled=false" splunk-otel-collector-chart/splunk-otel-collector --namespace splunk-otel 
```

and an example of using the -f argument 

```cmd
helm upgrade splunk-otel-collector -f {{path-to-values.yaml}} splunk-otel-collector-chart/splunk-otel-collector --namespace system-splunk-otel
```

It is possible to use them both together passing some values through the set argument and other through the values.yaml file

##### Create a base values.yaml 

- With default settings from the chart

````cmd
helm show values splunk-otel-collector-chart/splunk-otel-collector > values.yaml
````

**OR**

- With existing user specified settings from the release

```cmd
helm get values splunk-otel-collector --namespace splunk-otel >> values.yaml
```

#### Add or update values 

Ensure that these values are specified and update them and [any additional values](https://github.com/signalfx/splunk-otel-collector-chart/blob/main/docs/advanced-configuration.md) as appropriate to your environment.
- clusterName
- certmanager.enabled
- cloudProvider
- distribution
- environment
- gateway.enabled
- agent.discovery.enabled
- operator.enabled
- splunkObservability.accessToken
- splunkObservability.realm
- splunkPlatform.endpoint
- splunkPlatform.token
- splunkPlatform.index
- logsEngine: otel

Sample values.yaml file.
```yaml
certmanager:
  enabled: true
cloudProvider: azure
clusterName: my-k8s-cluster
distribution: aks
environment: prod
gateway:
  enabled: false
operator:
  enabled: true
splunkObservability:
  accessToken: xxx
  profilingEnabled: true
  realm: us1
splunkPlatform:
  endpoint: https://http-inputs-o11y-workshop-us1.splunkcloud.com/services/collector/event
  index: k8s_app_logs
  token: xxx
logsEngine: otel
```
## Run pre requisite checks

### Check the helm deployment status

```cmd
helm ls --namespace splunk-otel
```

### Check resources created by Helm deployment

```cmd
kubectl get pods --namespace splunk-otel
```

### Retrieve OTel pod logs for review

```cmd
kubectl logs -f {{otel-agent-pod}} --namespace splunk-otel
```

## Upgrade splunk-otel-collector release with updated values

### Execute helm upgrade 

```cmd
helm upgrade splunk-otel-collector -f values.yaml splunk-otel-collector-chart/splunk-otel-collector --namespace splunk-otel
```

### Review configmap to see the rendered pipeline

```cmd
kubectl get configmap splunk-otel-collector-otel-agent -o yaml --namespace splunk-otel
```