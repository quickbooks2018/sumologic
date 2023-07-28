# Sumologic

- Helm https://github.com/open-telemetry/opentelemetry-helm-charts/tree/main/charts/opentelemetry-collector

- Installing the Chart

- Add OpenTelemetry Helm repository:

```bash
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts
```

- OpenTelemetry Operator Helm Chart    
```bash
helm upgrade --install --namespace opentelemetry-operator-system \
  opentelemetry-operator open-telemetry/opentelemetry-operator
```


- Open-Telemetry Collector Installation  
- Where the mode value needs to be set to one of daemonset, deployment or statefulset.

```bash
helm upgrade -- install my-opentelemetry-collector open-telemetry/opentelemetry-collector --set mode=daemonset
```

- Sumologic kubenrtes helm chart https://github.com/SumoLogic/sumologic-kubernetes-collection

- https://github.com/SumoLogic/sumologic-kubernetes-collection/blob/release-v3.10/docs/README.md

- https://www.youtube.com/watch?v=lLRtK1FaTgM
  
```
helm repo add sumologic https://sumologic.github.io/sumologic-kubernetes-collection
helm repo ls
helm search repo sumologic
helm search repo sumologic/sumologic --versions
helm search repo sumologic/sumologic --version 3.10.0
helm show values sumologic/sumologic --version 3.10.0
helm show values sumologic/sumologic --version 3.10.0 > sumologic-values.yaml
helm repo update
```

- Sumologic otel Traces & auto instrumentation
- https://help.sumologic.com/docs/apm/traces/get-started-transaction-tracing/opentelemetry-instrumentation/kubernetes/
- https://help.sumologic.com/docs/apm/traces/get-started-transaction-tracing/set-up-traces-collection-for-kubernetes-environments/

```bash
helm dependency update
```
```bash
helm upgrade --install collection sumologic/sumologic \
--version 3.10.0 \
--namespace sumologic \
--create-namespace \
--set sumologic.accessId="abc" \
--set sumologic.accessKey="abc" \
--set sumologic.clusterName="kubernetes-eks-dev" \
--set sumologic.collectorName="kubernetes-2023-07-23T06:42:35.748Z" \
--set sumologic.setup.monitors.enabled=false \
--set sumologic.traces.enabled=true \
--set opentelemetry-operator.enabled=true \
--set opentelemetry-operator.createDefaultInstrumentation=true \
--set opentelemetry-operator.instrumentationNamespaces="default\,kube-system"
```
- NodeJs
```bash
helm upgrade --install collection sumologic/sumologic \
--version 3.10.0 \
--namespace sumologic \
--create-namespace \
--set sumologic.accessId="abc" \
--set sumologic.accessKey="abc" \
--set sumologic.clusterName="kubernetes-eks-dev" \
--set sumologic.collectorName="kubernetes-2023-07-23T06:42:35.748Z" \
--set sumologic.setup.monitors.enabled=false \
--set sumologic.traces.enabled=true \
--set opentelemetry-operator.enabled=true \
--set opentelemetry-operator.createDefaultInstrumentation=true \
--set opentelemetry-operator.instrumentation.nodejs.traces.enabled=true \
--set opentelemetry-operator.instrumentationNamespaces="default\,kube-system"
```

- Deployment with Minimal Resources
  - 
https://help.sumologic.com/docs/observability/kubernetes/quickstart/#:~:text=Resource%20requirements%E2%80%8B,Kubernetes%20Helm%20Chart%20is%20deployed.
```
helm upgrade --install collection sumologic/sumologic \
--version 3.10.0 \
--namespace sumologic \
--create-namespace \
--set sumologic.accessId="abc" \
--set sumologic.accessKey="abc" \
--set sumologic.clusterName="kubernetes-eks-dev" \
--set sumologic.collectorName="kubernetes-2023-07-23T06:42:35.748Z" \
--set sumologic.setup.monitors.enabled=false \
--set sumologic.traces.enabled=true \
--set opentelemetry-operator.enabled=true \
--set opentelemetry-operator.createDefaultInstrumentation=true \
--set opentelemetry-operator.instrumentation.nodejs.traces.enabled=true \
--set opentelemetry-operator.instrumentationNamespaces="default\,kube-system" \
--set metadata.metrics.statefulset.replicaCount=1 \
--set metadata.metrics.statefulset.resources.requests.memory=100Mi \
--set metadata.metrics.statefulset.resources.requests.cpu=50m
```

- Custom Storage Class
- https://github.com/kubernetes-sigs/aws-ebs-csi-driver/blob/master/examples/kubernetes/dynamic-provisioning/manifests/storageclass.yaml
  

- Adding annotation in custom namespace
```bash
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-java=true
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-python=true
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-nodejs=true
```
- Removing annotation is custom namespace
```bash
kubectl annotate namespace nodejs instrumentation.opentelemetry.io/inject-nodejs-
```

- WhiteBox & BlackBox Instrumentation
- https://github.com/quickbooks2018/sumologic/blob/master/open-telemetry.png
- https://github.com/quickbooks2018/sumologic/blob/master/Instrumentation.png

- Opentelemetry Auto-Instrumentation this will launch a OTEL container in a pod [label deployment (Required)]
- Label
```label
kubectl label deployment example-app Instrumentation.opentelemetry.io: "true" # Enable OpenTelemetry
  
Instrumentation.opentelemetry.io: "true" # Enable OpenTelemetry
```
- Annotation required in deployment
```annotation
instrumentation.opentelemetry.io/inject-python: "true"  
```
