# Annotations

## What are annotations
Annotations are designed to help with setting sourcetypes or indexes and applying exclusions to the things you don’t want to log
Available annotations:
- splunk.com/index
- splunk.com/sourcetype
- splunk.com/exclude

## Where can i use them?

- Any object that creates pods
  - Deployment
  - Daemonset
  - Replicaset
  - Statefulset
- Namespaces 
  (splunk.com/index or splunk.com/exclude annotations only)

## Commands

### Add index or sourcetype annotation
```cmd
kubectl annotate <name of object/namespace to be annotated> splunk.com/<index | sourcetype>=<value>
```

### Add exclude annotation
```cmd
kubectl annotate <name of object/namespace to be annotated> splunk.com/exclude=true
```

### Remove the annotation from this deployment
```cmd
kubectl annotate <name of object/namespace to be annotated> splunk.com/<index | sourcetype | exclude>-
```

