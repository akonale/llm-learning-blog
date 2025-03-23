---
title: Getting Started
nav_order: 2
has_children: true
---

## Getting Started
GPTs, LLaMAs, DeepSeek models, Agentic AI, RAGs—there’s an explosion of tools, coding interfaces, and workflows in the LLM space. 
It’s easy to feel lost. Where do I even start? More importantly, what am I trying to achieve?

### My Goals
- Develop a solid understanding of what LLMs can and cannot do.
- Understand the various abstractions used to build applications on top of LLMs.
- Gain fundamental knowledge of how to use these abstractions effectively.

### A Crude Understanding of LLMs
Before diving into building, I needed a foundational grasp of what LLMs actually are. A great starting point for me was this Stanford lecture on LLMs:
📺 [Watch here](https://youtu.be/9vM4p9NN0Ts?si=wfodv8VI5Z3I5F5m)

I don’t have a strong background in ML, but I still found this lecture helpful. 
At first, I didn’t understand a lot of what was being discussed. 
However, as I progressed, I started recalling concepts from this lecture and came back to it for reference.

### Interacting with LLMs
Most applications today don’t train their own models—they use hosted LLMs provided by companies like OpenAI, Google, and others. 
These models are accessible via APIs, allowing developers to integrate AI-powered features into their applications.

1. *Chat with OpenAI's GPT Model*
Using OpenAI’s [API](https://platform.openai.com/docs/overview), you can interact with a GPT model like this:

```py
# 1. You need to install openai via pip
# 2. Create account in open api platform and get api keys
import openai

client = OpenAI(
     api_key='YOUR_API_KEY',
)
response = openai.ChatCompletion.create(
    model="gpt-4",
    messages=[{"role": "user", "content": "What is the capital of France?"}]
)

print(response["choices"][0]["message"]["content"])
```

2. *Chat with Google's Gemini Model*
Google provides its own [API for Gemini](https://ai.google.dev/gemini-api/docs), and the interaction looks slightly different:

```py
import google.generativeai as genai

genai.configure(api_key="YOUR_GOOGLE_API_KEY")

model = genai.GenerativeModel("gemini-pro")
response = model.generate_content("What is the capital of France?")

print(response.text)
```

### Standardizing LLM APIs
As you can see, the SDKs and function calls are slightly different, but fundamentally, they are both API requests.
If every model provider exposed a completely different interface, integration would become chaotic.
In software development, this problem is often solved by API specifications—a common standard that different services adhere to. 
The OpenAI API specification has become one of the most widely adopted standards for interacting with LLMs.
Using the OpenAI API specification and its Python SDK, developers can write code that interacts uniformly with models from different companies. 
This abstraction makes it easier to switch between providers without rewriting large portions of code.

1. *Google gemini model with openai compatibility*

```python
from openai import OpenAI

client = OpenAI(
    api_key="GEMINI_API_KEY",
    base_url="https://generativelanguage.googleapis.com/v1beta/openai/"
)

response = client.chat.completions.create(
    model="gemini-2.0-flash",
    messages=[
        {
            "role": "user",
            "content": "What is the capital of France?"
        }
    ]
)

print(response.choices[0].message)
```
As you can see, we can interact with gemini with openai. Notice that `base_url` and `model` parameter changes with this.

### Next: Local setup
Running models is not cheap. Using APIs will cost me. But now I start noticing that LLMs are just another tool. eg. like a database.
I like to run tools and develop locally. So why don't I install LLMs locally and write apps on top of it. Lets explore it next.

- [Local setup](./docs/local_setup.md)

## Key takeaways
1. Largely models are interacted with http apis. This is the first level of abstraction on top of model.
2. open-ai spec is a popular http spec. 
3. There are many libraries out there to interact with models. eg. langchain. Not diving too deep into it now.