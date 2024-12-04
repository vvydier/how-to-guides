# Ingest log files on linux with Open Telemetry Collector using `filelog` receiver

The OpenTelemetry Collector can ingest logs using a Filelog receiver and the Splunk HEC exporter. 

## Open Telemetry Log Receiver Configuration

We will need to edit the collectors configuration file to add a new filelog reciever:

```cmd
sudo vi /etc/otel/collector/agent_config.yaml
```

Under receivers: create the Filelog Receiver (make sure to indent correctly):

```
receivers:
    filelog:
        include: [/home/ubuntu/workshop/petclinic/petclinic.log]
```

The under the service: section, find the logs: pipeline, replace fluentforward with filelog and optionally remove otlp (again, make sure to indent correctly):

```
    logs:
        receivers: [filelog]
```

Save the file and exit the editor.


## Open Telemetry Log Exporter Configuration

The Splunk distribution of Open Telemetry Collector is already setup to use the SPLUNK HEC exporter for logs.
We just need to update the HEC Token and HEC URL environment values for the collector to use. 

Edit the /etc/otel/collector/splunk-otel-collector.conf file.

```cmd
sudo vi /etc/otel/collector/splunk-otel-collector.conf
```

Replace the values for SPLUNK_HEC_URL and SPLUNK_HEC_TOKEN 

## Restart Open Telemetry Collector 

Restart the collector to apply the changes.

```cmd
sudo systemctl restart splunk-otel-collector
```
