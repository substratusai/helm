# vLLM Helm Chart
A Helm chart for deploying vLLM.
vLLM is a fast and easy-to-use library for LLM inference and serving.

## Usage

Basic usage:
```bash
helm repo add substratusai https://substratusai.github.io/helm
# Note by default the resource limit is set to 1 GPU
helm install mistral-7b-instruct substratusai/vllm \
  --set model=mistralai/Mistral-7B-Instruct-v0.1
```

### Mistral 7B Instruct on GCP targeting 1 x L4 GPU

Create a file named `values.yaml` with following content:

[embedmd]:# (examples/mistral-7b-instruct-gcp-l4.yaml)
```yaml
model: mistralai/Mistral-7B-Instruct-v0.1
resources:
  limits:
    nvidia.com/gpu: 1
nodeSelector:
  cloud.google.com/gke-accelerator: nvidia-l4
```

Install using Helm:
```bash
helm install mistral-7b-instruct substratusai/vllm \
    -f values.yaml
```

### Mistral 7B Instruct on GKE Autopilot with ReadManyOnly PVC to store model
Create a K8s Job to load the model into a PVC:
```bash
kubectl apply -f https://raw.githubusercontent.com/substratusai/helm/main/charts/vllm/examples/load-model-job-mistral-7b-instruct.yaml
```

Create a file named `values.yaml` with following content:

[embedmd]:# (examples/readmanypvc-gke-autopilot-values.yaml)
```yaml
# This example requires first running
# `kubectl apply -f load-model-job-mistral-7b-instruct.yaml` to load the model into a PVC.
model: /model

replicaCount: 0

env:
- name: SERVED_MODEL_NAME
  value: mistral-7b-instruct-v0.1

deploymentAnnotations:
  lingo.substratus.ai/models: mistral-7b-instruct-v0.1
  lingo.substratus.ai/min-replicas: "0" # needs to be string
  lingo.substratus.ai/max-replicas: "3" # needs to be string

readManyPVC:
  enabled: true
  sourcePVC: "mistral-7b-instruct"
  mountPath: /model
  size: 20Gi

nodeSelector:
  cloud.google.com/gke-accelerator: nvidia-l4

resources:
  requests:
    cpu: 7
    memory: 24Gi
    ephemeral-storage: 10Gi
```

Install using Helm:
```bash
helm install mistral-7b-instruct substratusai/vllm \
    -f values.yaml
```

### Mistral 7B instruct quantized using awq

Create a file named `values.yaml` with following content:
[embedmd]:# (examples/mistral-7b-awq-values.yaml)
```yaml
model: TheBloke/Mistral-7B-Instruct-v0.1-AWQ
quantization: awq
dtype: half
maxModelLen: 4096
```

Install using Helm:
```bash
helm install mistral-7b-instruct-awq substratusai/vllm \
    -f values.yaml
```

## Default Values

Take a look at the default `values.yaml`:

[embedmd]:# (values.yaml)
```yaml
replicaCount: 1
# Change this if you want to serve another model
model: mistralai/Mistral-7B-Instruct-v0.1
quantization: "" # optional, choose awq or squeezellm
dtype: "" # optional, only required to be set to half when using awq for quantization
gpuMemoryUtilization: "" # Optional, default is 0.90

# this only works on GKE today
readManyPVC:
  enabled: false
  # provide the name of the PVC that has the model
  sourcePVC: ""
  accessModes:
  - ReadOnlyMany
  mountPath: /model
  size: 30Gi
  # storageClass needs to match the sourcePVC storageClass
  storageClass: ""

deploymentAnnotations: {}

# Override the resources if you need more
resources:
  requests:
    cpu: 500m
    memory: "512Mi"
  limits:
    nvidia.com/gpu: 1


# Override env variables
env: {}
port: 8080

# Add nodeSelectors to target specific GPU types
nodeSelector: {}
#  E.g. for GCP L4 cloud.google.com/gke-accelerator: nvidia-l4

image:
  repository: ghcr.io/substratusai/vllm
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: ""

imagePullSecrets: []
nameOverride: ""
fullnameOverride: ""

podAnnotations: {}

podSecurityContext: {}

securityContext: {}

service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  className: ""
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  hosts:
    - host: chart-example.local
      paths:
        - path: /
          pathType: ImplementationSpecific
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

tolerations: []

affinity: {}
```
