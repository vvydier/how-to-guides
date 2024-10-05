# Update and upgrade Splunk k8s Otel collector configuration

### Create the values.yaml file with custom configuration

A. Start with a base values.yaml with the default settings, and update and customize as required
````cmd
helm show values splunk-otel-collector-chart/splunk-otel-collector > values.yaml
````

Most useful attributes to update are
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

B. Any of these values can also be supplied by --set parameter

```cmd
--set="cloudProvider=azure,distribution=aks,splunkObservability.accessToken=$ACCESS_TOKEN,clusterName=my-kube-cluster,splunkObservability.realm=us0,gateway.enabled=false,splunkPlatform.endpoint=https://http-inputs-myorg.splunkcloud.com:443,splunkPlatform.token=$HEC_TOKEN,splunkObservability.profilingEnabled=true,environment=production,operator.enabled=true,certmanager.enabled=true,agent.discovery.enabled=false" 
```

C. Previously supplied values can be retrieved and saved to yaml file. This file can then be updated and used to upgrade collector.

```cmd
helm get values splunk-otel-collector --namespace otel >> user_values.yaml
```

D. Add [any additional properties](https://github.com/signalfx/splunk-otel-collector-chart/blob/main/docs/advanced-configuration.md) to values.yaml file 


### Check the helm deployment status

```cmd
helm ls --namespace system-splunk-otel
```

### Check resources created by Helm deployment

```cmd
kubectl get pods --namespace system-splunk-otel
```

### Retrieve OTel pod logs for review

```cmd
kubectl logs -f {{otel-agent-pod}} --namespace system-splunk-otel
```

### Update your deployment with changes to values.yaml

```cmd
helm upgrade splunk-otel-collector -f values.yaml splunk-otel-collector-chart/splunk-otel-collector --namespace system-splunk-otel
```

### Review configmap to see the rendered pipeline

```cmd
kubectl get configmap splunk-otel-collector-otel-agent -o yaml --namespace system-splunk-otel
```