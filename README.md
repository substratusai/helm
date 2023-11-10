# Substratus AI Helm Charts
A collection of exquisitely crafted helm charts for LLMs:
- vLLM
- Text Generation Inference
- Lingo

## Enable Substratus Helm repo
```bash
helm repo add substratusai https://substratusai.github.io/helm
helm repo update
```

## Usage guides

### vLLM
Basic usage:
```bash
# Note by default the resource limit is set to 1 GPU
helm install mistral-7b-instruct substratusai/vllm \
  --set model=mistralai/Mistral-7B-Instruct-v0.1
```
For Advanced usage see: [vLLM Chart Guide](https://github.com/substratusai/helm/blob/main/charts/vllm/README.md)

