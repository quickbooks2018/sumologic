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
helm repo add sumologic https://sumologic.github.io/sumologic-kubernetes-collection
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
--set opentelemetry-operator.instrumentationNamespaces="default\,example-app"
```
- NodeJs
```bash
helm repo add sumologic https://sumologic.github.io/sumologic-kubernetes-collection
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
--set opentelemetry-operator.instrumentationNamespaces="default\,example-app"
```

- Deployment with Minimal Resources
- https://help.sumologic.com/docs/observability/kubernetes/quickstart/#:~:text=Resource%20requirements%E2%80%8B,Kubernetes%20Helm%20Chart%20is%20deployed.
```
helm repo add sumologic https://sumologic.github.io/sumologic-kubernetes-collection
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
--set opentelemetry-operator.instrumentationNamespaces="default\,example-app" \
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
- Sample App with Istio
```deployment
apiVersion: v1
kind: Namespace
metadata:
  name: example-app
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: python-basic-secret
  namespace: example-app
  labels:
    app: basic-secret
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-basic-secret-env
  namespace: example-app
  labels:
    Instrumentation.opentelemetry.io: "true" # Enable OpenTelemetry
    app: basic-secret
spec:
  selector:
    matchLabels:
      app: basic-secret
  replicas: 1
  template:
    metadata:
      annotations:
        instrumentation.opentelemetry.io/inject-python: "true"
        vault.hashicorp.com/agent-init-first: 'true'
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/tls-skip-verify: "true"
        vault.hashicorp.com/agent-inject-secret-helloworld: "secret/basic-secret/helloworld"
        vault.hashicorp.com/agent-inject-template-helloworld: |
          {{ with secret "secret/data/basic-secret/helloworld" -}}
          export USERNAME="{{ .Data.data.username }}"
          export PASSWORD="{{ .Data.data.password }}"
          {{- end }}
        vault.hashicorp.com/role: "basic-secret-role"
      labels:
        app: basic-secret
    spec:
      serviceAccountName: basic-secret
      containers:
        - name: app
          image: quickbooks2018/python:metrics
          command: ["/bin/sh", "-c"]
          args:
            - |
              sleep 3
              echo ">> creds from vault"
              if [ -f /vault/secrets/helloworld ]; then
                cat /vault/secrets/helloworld
                . /vault/secrets/helloworld
              fi
              "python" "metrics.py"
``` 

- Istio Helm Chart SumoLogic
```helm
helm repo add sumologic https://sumologic.github.io/sumologic-kubernetes-collection
helm upgrade --install collection sumologic/sumologic \
--version 3.10.0 \
--namespace sumologic \
--create-namespace \
--set sumologic.accessId=abc \
--set sumologic.accessKey=abc \
--set sumologic.clusterName="eks-dev" \
--set sumologic.collectorName="eks-dev" \
--set sumologic.setup.monitors.enabled=false \
--set sumologic.traces.enabled=true \
--set opentelemetry-operator.enabled=true \
--set opentelemetry-operator.createDefaultInstrumentation=true \
--set opentelemetry-operator.instrumentation.nodejs.traces.enabled=true \
--set opentelemetry-operator.instrumentationNamespaces="default\,nodejs\,example-app\,golang" \
--set metadata.metrics.statefulset.replicaCount=1 \
--set metadata.metrics.statefulset.resources.requests.memory=100Mi \
--set metadata.metrics.statefulset.resources.requests.cpu=50m \
--set meshConfig.enableTracing=true \
--set meshConfig.defaultConfig.tracing.openCensusAgent.address=collection-otelagent.sumologic:55678
```

- Istio Installation with values yaml (meshConfig) https://github.com/quickbooks2018/kubernetes-istio.git

- Automatic tracing https://opentelemetry.io/docs/kubernetes/operator/automatic/
