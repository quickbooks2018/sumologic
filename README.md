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

- Adding annotation in custom namespace
```bash
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-java=true
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-python: "true"
kubectl annotate namespace my-namespace instrumentation.opentelemetry.io/inject-nodejs: "true"
```

- WhiteBox & BlackBox Instrumentation
- https://github.com/quickbooks2018/aws/blob/master/open-telemetry.png
- 
