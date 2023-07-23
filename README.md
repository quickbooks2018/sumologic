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


```
helm repo add sumologic https://sumologic.github.io/sumologic-kubernetes-collection
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
--namespace sumologic \
--create-namespace \
--set sumologic.accessId="suWmfM14JgEI1O" \
--set sumologic.accessKey="53q4FiAfNdlxUqCdSV9lESKOi4KtzxBKMmIOaXkR2qB79a4h7ktEAFcniF7oaw39" \
--set sumologic.clusterName="cloudgeeks-dev" \
--set sumologic.traces.enabled=true \
--set opentelemetry-operator.enabled=true \
--set opentelemetry-operator.createDefaultInstrumentation=true \
--set opentelemetry-operator.instrumentationNamespaces="default\,kube-system"
```

```bash
helm upgrade --install collection sumologic/sumologic \
  --namespace=sumologic \
  --create-namespace \
  --set sumologic.accessId='suWmfM14JgEI1O' \
  --set sumologic.accessKey='53q4FiAfNdlxUqCdSV9lESKOi4KtzxBKMmIOaXkR2qB79a4h7ktEAFcniF7oaw39' \
  --set sumologic.clusterName=Kubernetes_cluster_cloudgeeks-dev  \
  --set sumologic.collectorName=cloudgeeks-eks-dev \
  --set sumologic.setup.monitors.enabled=false
```

- Adding annotation in custom namespace
```bash
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-java=true
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-python: "true"
instrumentation.opentelemetry.io/inject-nodejs: "true"
```
