# Troubleshoot the Splunk Open Telemetry Collector on Kubernetes

## [Verify all the OpenTelemetry resources are deployed successfully](https://docs.splunk.com/observability/en/gdi/opentelemetry/automatic-discovery/k8s/k8s-backend.html#verify-all-the-opentelemetry-resources-are-deployed-successfully)

Resources include the Collector, the Operator, webhook, and instrumentation. Run the following commands to verify the resources are deployed correctly.

The pods running in the collector namespace must include the following:

```
kubectl get pods
# NAME                                                          READY
# NAMESPACE     NAME                                                            READY   STATUS
# monitoring    splunk-otel-collector-agent-lfthw                               2/2     Running
# monitoring    splunk-otel-collector-cert-manager-6b9fb8b95f-2lmv4             1/1     Running
# monitoring    splunk-otel-collector-cert-manager-cainjector-6d65b6d4c-khcrc   1/1     Running
# monitoring    splunk-otel-collector-cert-manager-webhook-87b7ffffc-xp4sr      1/1     Running
# monitoring    splunk-otel-collector-k8s-cluster-receiver-856f5fbcf9-pqkwg     1/1     Running
# monitoring    splunk-otel-collector-opentelemetry-operator-56c4ddb4db-zcjgh   2/2     Running
```

The webhooks in the collector namespace must include the following:

```
kubectl get mutatingwebhookconfiguration.admissionregistration.k8s.io
# NAME                                      WEBHOOKS   AGE
# splunk-otel-collector-cert-manager-webhook              1          14m
# splunk-otel-collector-opentelemetry-operator-mutation   3          14m
```

The instrumentation in the collector namespace must include the following:

```cmd
kubectl get otelinst
# NAME                          AGE   ENDPOINT
# splunk-instrumentation        3m   http://$(SPLUNK_OTEL_AGENT):4317
```

## [Set annotations to instrument applications](https://docs.splunk.com/observability/en/gdi/opentelemetry/automatic-discovery/k8s/k8s-backend.html#set-annotations-to-instrument-applications)

```cmd
kubectl patch deployment <my-deployment> -n <my-namespace> -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-<application_language>":"monitoring/splunk-otel-collector"}}}}}'
```

For example, for java app, with splunk-otel-collector installed in splunk-otel namespace.
```cmd
kubectl patch deployment petclinic-deployment -n app-ns -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-java":"splunk-otel/splunk-otel-collector"}}}}}'
```

## [Verify instrumentation](https://docs.splunk.com/observability/en/gdi/opentelemetry/automatic-discovery/k8s/k8s-backend.html#verify-instrumentation)

To verify that the instrumentation was successful, run the following command on an individual pod:

```cmd
kubectl describe pod <application_pod_name> -n <namespace>
```

The instrumented pod contains an initContainer named opentelemetry-auto-instrumentation and the target application container should have several OTEL_* environment variables

##[Configure the instrumentation](https://docs.splunk.com/observability/en/gdi/opentelemetry/automatic-discovery/k8s/k8s-backend.html#optional-configure-the-instrumentation)

At a minimum, instrumented services require the OTEL_SERVICE_NAME and OTEL_RESOURCE_ATTRIBUTES values to be specified.

These can be setup

```cmd
kubectl set env deployment/<my-deployment> OTEL_SERVICE_NAME=petclinic
```

```cmd
kubectl set env deployment/<my-deployment> OTEL_RESOURCE_ATTRIBUTES=deployment.environment=prod,build.id=nov2024_v2
```