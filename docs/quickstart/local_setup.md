---
title: Run LLM locally
parent: Getting Started
nav_order: 2
---

# Motivation: Why Run LLMs Locally?
Using hosted LLM APIs is convenient, but it can get expensive quickly, especially when working with advanced models. The cost depends on factors like token usage and model complexity.

However, after some exploration, I started seeing LLMs as just another tool—similar to a database. And just like with databases, the best way to understand and experiment with them is by installing and running them locally.

## Exploring Local Installation
What does it mean to install an LLM locally? At its core, it involves:

1. Downloading a model – A file containing the trained neural network weights.
2. Running inference framework which exposes api (I want open-ai compatible) accepts input (a prompt) into the model and getting an output (a response).

> **What is inference?**: 
> Inference is the process where a trained model takes an input, processes it, and generates an output. For example, when you send a text prompt to an LLM, the model runs inference to generate a response.

## Choosing a Framework
There are multiple frameworks available for running LLMs locally. Some popular ones include:
- Llama.cpp – Lightweight, optimized for running LLaMA models efficiently on CPUs.
- vLLM – Optimized for high-throughput inference on GPUs, used in production environments.
- Ollama – Simplifies running models locally with an easy-to-use CLI. 
- docker - At the time of writing, docker [announced](https://www.docker.com/llm/) to release support. This will be super exciting. Not available yet. 

Since I wanted something Docker-friendly, I decided to start with vLLM. It’s widely used for efficient LLM inference and integrates well with different models.

## Where to download model from ?
After researching a bit, I found that most models are available on [Hugging Face](https://huggingface.co/). Its a 
community driven platform for sharing machine learning models. It has a vast collection of pre-trained models, including LLMs.
Optionally you can run inferece servers on huggingface. But lets run it locally first.

## Experience with vLLM
1. Tried running vLLM on docker. https://docs.vllm.ai/en/stable/deployment/docker.html (did not work on my mac)
2. Tried building vLLM docker from source. https://github.com/vllm-project/vllm/blob/main/docs/build_docker.md (did not work on my mac)
3. Tried installing directly using pip/python. https://docs.vllm.ai/en/v0.6.2/getting_started/installation.html (This worked, but had lot of trouble getting it working with a few models)


```shell
# This downloads model from huggingface and runs it locally
vllm serve deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B

# This runs the swagger-ui on port 8000
# http://localhost:8000/docs
```

5. I ran my first inference server locally ! But still gave up on vLLM :(. It seems vLLM is not a great option for macOS.

## Ollama
After spending some time in [LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/) , I decided to try [Ollama](https://ollama.ai/).
This worked out of the box on my mac. After installing ollama runs in port:11434. Its as simple as,

```shell
ollama run llama3.2
# Or running deepseek model locally
ollama run hf.co/unsloth/DeepSeek-R1-Distill-Qwen-1.5B-GGUF:Q2_K
```

Now you can interact with the model via curl/python etc.
```python
client = OpenAI(
    base_url = 'http://localhost:11434/v1',
    api_key='ollama', # required, but unused
)
response = client.chat.completions.create(
    model=MODEL_ID,
    messages=[
        {"role": "user", "content": "What is the capital of France ?"},
    ],
    max_tokens=32000,
)
print(response.choices[0].message.content)
```
Life was super easy after installing ollama.

## Running Other Models Locally with Ollama  

After spending a few hours on Discord, Reddit, and experimenting with different setups, here’s what I discovered about running various models locally.  

### 1. Finding the Right Model on Hugging Face  
If you want to run a model locally, start by **finding it on Hugging Face**. However, consider:  
- **Model size** – You **cannot** run a 70B model on a consumer-grade laptop. A 1.5B or 7B model is more practical.  
- **Hardware requirements** – Some models require a GPU, while others can run on a CPU with optimizations.  

### 2. Different Versions of the Same Model  
Many models come in multiple versions. For example, DeepSeek AI has:  
- **`deepseek-ai/DeepSeek-R1-Distill-Qwen-1.5B`** (small, runs on consumer hardware)  
- **`deepseek-ai/DeepSeek-R1-Distill-Qwen-70B`** (large, requires enterprise-level hardware)  

Each framework has different compatibility:  
- **vLLM or llama.cpp** – Can run models like **DeepSeek 1.5B** efficiently.  
- **Ollama** – Only runs **GGUF-formatted models**.  eg. [hf.co/unsloth/DeepSeek-R1-Distill-Qwen-1.5B-GGUF:Q2_K](https://huggingface.co/unsloth/DeepSeek-R1-Distill-Qwen-1.5B-GGUF)

> **Quick Note on GGUF:**  
> GGUF (GPT-Generated Unified Format) is a file format optimized for running LLMs efficiently, especially on consumer hardware. Many models are available in GGUF format for Ollama.  

### 3. Using Python APIs for Interoperability  
Ollama provides its own Python API, but for flexibility, I typically use **OpenAI's Python API** to interact with different models. This allows a uniform interface when switching between local and hosted models.

## Key takeaways
- **Hugging Face** is the best place to find models.
- **vLLM**, **Ollama**, **llamaCPP** are some popular frameworks for running models locally which provide openai-compatible server.
- **vLLM**, **TGI** etc are used in production grade hardware. ( haven't tried this )

## References
- [LocalLLaMA](https://www.reddit.com/r/LocalLLaMA/)
- [Ollama Discord](https://discord.com/invite/ollama)
- [Hugging Face](https://huggingface.co/)
- [Ollama openai api](https://ollama.com/blog/openai-compatibility)