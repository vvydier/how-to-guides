# Install the Splunk Open Telemetry Collector on Kubernetes

## Open Telemetry Collector

The OpenTelemetry Collector is the core component of instrumenting infrastructure and applications. Its role is to
collect and send:

* Infrastructure metrics (disk, cpu, memory, etc)
* Application Performance Monitoring (APM) traces
* Profiling data
* Host and application logs


## Pre-requisites

- Create indexes as needed
- Create HEC endpoint and token
- Create namespace for the opentelemetry collector components

```cmd
kubectl create ns splunk-otel
```


## Install
1. Make sure that you have installed and configured the Helm 3.0 client. For more information see the [version skew supported between Helm and Kubernetes](https://helm.sh/docs/topics/version_skew/)

2. Add the Splunk OpenTelemetry Collector for Kubernetes' Helm chart repository

```cmd
helm repo add splunk-otel-collector-chart https://signalfx.github.io/splunk-otel-collector-chart
```

3. Ensure the latest state of the repository

```cmd
helm repo update
```

4. Install the Splunk OpenTelemetry Collector for Kubernetes with the desired configuration values

- Check if a cert-manager is already installed by looking for cert-manager pods.
```cmd
kubectl get pods -l app=cert-manager --all-namespaces
```

- If cert-manager is deployed, make sure to remove certmanager.enabled=true from the list of values to set.
```cmd
helm install splunk-otel-collector --version 0.111.0 --set="cloudProvider=azure,distribution=aks,splunkObservability.accessToken=$ACCESS_TOKEN,clusterName=my-kube-cluster,splunkObservability.realm=us0,gateway.enabled=false,splunkPlatform.endpoint=https://http-inputs.myorg.splunkcloud.com/services/collector,splunkPlatform.token=$HEC_TOKEN,splunkObservability.profilingEnabled=true,environment=production,operator.enabled=true,certmanager.enabled=true,agent.discovery.enabled=false" splunk-otel-collector-chart/splunk-otel-collector
```

See [advanced configuration](https://github.com/signalfx/splunk-otel-collector-chart/blob/main/docs/advanced-configuration.md) for more values and settings

5. Set annotations to activate the automatic instrumentation before runtime. For more information, [see the documentation](https://docs.splunk.com/observability/en/gdi/opentelemetry/automatic-discovery/k8s/k8s-backend.html#set-annotations-to-instrument-applications)

Note: If your chart deployment is in a namespace other than default, replace default in the annotation with the namespace you're using for deployment.

Java
```cmd
kubectl patch deployment <my-deployment> -n <my-namespace> -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-java":"default/splunk-otel-collector"}}}}}'
```

.NET 

For linux-x64
```cmd
kubectl patch deployment <my-deployment> -n <my-namespace> -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-dotnet":"default/splunk-otel-collector","instrumentation.opentelemetry.io/otel-dotnet-auto-runtime":"linux-x64"}}}}}'
```

For linux-musl-x64
```cmd
kubectl patch deployment <my-deployment> -n <my-namespace> -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-dotnet":"default/splunk-otel-collector","instrumentation.opentelemetry.io/otel-dotnet-auto-runtime":"linux-musl-x64"}}}}}'
```

Node.js
```cmd
kubectl patch deployment <my-deployment> -n <my-namespace> -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-nodejs":"default/splunk-otel-collector"}}}}}'
```

