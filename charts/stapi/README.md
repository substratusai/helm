# stapi Helm Chart

A Helm chart for deploying STAPI, an OpenAI compatible Embedding API using Sentence Transformers.

See [substratusai/stapi GitHub repo](https://github.com/substratusai/stapi) for more info.

## Usage

Basic usage:
```bash
helm repo add substratusai https://substratusai.github.io/helm
# Note by default CPU only is used
helm install stapi-minilm-l6-v2 substratusai/stapi \
  --set model=all-MiniLM-L6-v2
```

### Utilizing GPU and specifying a nodeSelector to target GPU family

Create a file named `values.yaml` with following content:

[embedmd]:# (examples/gpu.yaml)
```yaml
model: all-MiniLM-L6-v2
resources:
  limits:
    nvidia.com/gpu: 1
nodeSelector:
  cloud.google.com/gke-accelerator: nvidia-l4
```

Install using Helm:
```bash
helm install stapi-gpu substratusai/stapi \
    -f values.yaml
```

## Default Values

Take a look at the default `values.yaml`:

[embedmd]:# (values.yaml)
```yaml
replicaCount: 1
# Change this if you want to serve another model
model: all-MiniLM-L6-v2

# Override the resources if you need more
resources:
  requests:
    cpu: 500m
    memory: "512Mi"
#  limits:
#    nvidia.com/gpu: 1


# Override env variables
env: {}
port: 8080

# Add nodeSelectors to target specific GPU types
nodeSelector: {}
#  E.g. for GCP L4 cloud.google.com/gke-accelerator: nvidia-l4

image:
  repository: ghcr.io/substratusai/stapi
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
