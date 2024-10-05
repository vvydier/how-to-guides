## Add log files from Kubernetes host machines/volumes

### [Source: Ingest additional logs from k8s host machines and volumes](https://github.com/signalfx/splunk-otel-collector-chart/blob/main/docs/advanced-configuration.md#add-log-files-from-kubernetes-host-machinesvolumes)

You can add additional log files to be ingested from Kubernetes host machines and Kubernetes volumes by configuring `agent.extraVolumes`, `agent.extraVolumeMounts` and `logsCollection.extraFileLogs` in the values.yaml file used to deploy Splunk OpenTelemetry Collector for Kubernetes.

Example of adding audit logs from Kubernetes host machines

```yaml
logsCollection:
  extraFileLogs:
    filelog/audit-log:
      include: [/var/log/kubernetes/apiserver/audit.log]
      start_at: beginning
      include_file_path: true
      include_file_name: false
      resource:
        com.splunk.source: /var/log/kubernetes/apiserver/audit.log
        host.name: 'EXPR(env("K8S_NODE_NAME"))'
agent:
  extraVolumeMounts:
    - name: audit-log
      mountPath: /var/log/kubernetes/apiserver
  extraVolumes:
    - name: audit-log
      hostPath:
        path: /var/log/kubernetes/apiserver
```

