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

## Default Values

Take a look at the default `values.yaml`:

[embedmd]:# (values.yaml)
```yaml
replicaCount: 1
# Change this if you want to serve another model
model: mistralai/Mistral-7B-Instruct-v0.1

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