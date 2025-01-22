# Steps to create and delete a EKS cluster with petclinic

eksctl create cluster --name eks1 --region us-west-1
kubectl get all,cm,secret,ing -A

kubectl create ns splunk-otel
helm install splunk-otel-collector --version 0.111.0  -f values.yaml splunk-otel-collector-chart/splunk-otel-collector --namespace splunk-otel
k logs -f pod/splunk-otel-collector-agent-b5tks -n splunk-otel

kubectl get deployments -A

kubectl patch deployment <deployment-name> -n <namespace> -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-java":"splunk-otel/splunk-otel-collector"}}}}}'

kubectl create ns petclinic

kubectl apply -f petclinic-deployment.yaml -n petclinic
kubectl apply -f petclinic-service.yaml -n petclinic

kubectl describe pod petclinic-deployment -n petclinic

kubectl patch deployment petclinic-deployment -n petclinic -p '{"spec":{"template":{"metadata":{"annotations":{"instrumentation.opentelemetry.io/inject-java":"splunk-otel/splunk-otel-collector"}}}}}'

kubectl describe pod petclinic-deployment -n petclinic
kubectl describe pod splunk-otel-collector -n splunk-otel

kubectl get service -A
