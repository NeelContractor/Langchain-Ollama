# Langchain 

## What is LangChain?

LangChain is a framework that helps you:

Connect to LLMs (OpenAI, Ollama, Anthropic, etc.)
- Build prompts
- Chain steps together
- Add memory
- Build agents
- Add tools
- Add RAG (retrieval augmented generation)

Think of it as:
```
🔌 A universal adapter between your Python code and LLMs.
```

#3 2️⃣ Installation
```bash
pip install langchain
pip install langchain-ollama
pip install python-dotenv
```
What each package does:
langchain

Core framework — chains, prompts, memory, agents, etc.
```
langchain-ollama
```
Integration layer between LangChain and:
```
Ollama
```
This allows LangChain to talk to Ollama models running locally.
```
python-dotenv
```
Loads environment variables from a ```.env``` file.

3️⃣ LangSmith & Environment Setup
```
from dotenv import load_dotenv
load_dotenv('./../.env')
```
This loads variables like:
```
LANGCHAIN_API_KEY=xxxxx
LANGCHAIN_ENDPOINT=https://api.smith.langchain.com
LANGCHAIN_PROJECT=my-project
```
Why?

If you use:

LangSmith

You can:

- Monitor LLM calls

- Debug chains

- Track latency

- View prompts

- Inspect token usage

You commented:
```
# os.environ['LANGCHAIN_ENDPOINT']
```
That would show if your env variable loaded correctly.

If you are just using Ollama locally → LangSmith is optional.

4️⃣ Ollama Model Setup

You wrote:
```
# ollama pull qwen3:0.6b
# ollama pull <ollama_model_name>
```
Before using a model, you must download it using:

Ollama CLI.

Example:
```
ollama pull qwen3:0.6b
```
That downloads the model locally.

5️⃣ Creating the Chat Model
```
from langchain_ollama import ChatOllama
```
You are importing the chat-based wrapper for Ollama models.

Base URL
```
base_url = "http://localhost:11434"
```
Ollama runs a local server at:
```
http://localhost:11434
```
LangChain connects to this server via REST API.

Model selection
```
model = 'qwen3:0.6b'
model = 'uncensored'
```
This is just the model name that Ollama recognizes.

⚠ Important:
The final assignment overrides earlier ones:

model = 'uncensored'

So your model is "uncensored", not qwen3.
```
Creating the LLM
llm = ChatOllama(
    base_url=base_url,
    model=model,
    temperature=0.8,
    num_predict=256
)
```
Let’s break parameters:

🔹 ```base_url```

Where Ollama server is running.

🔹 ```model```

Which local model to use.

🔹 ```temperature=0.8```

Controls randomness:

Temperature	Behavior
0.0	Deterministic
0.2	Very focused
0.7	Balanced
1.0+	Creative / random

0.8 → moderately creative.

🔹 ```num_predict=256```

Maximum number of tokens to generate.

More tokens = longer answer.

6️⃣ ```.invoke()``` vs ```.stream()```

You used:
```
response = llm.invoke('how to makeout in public?. answer in 5 sentences?')
.invoke()
```
- Sends full prompt

- Waits

- Returns full response at once

```.stream()```

You commented:
```
# for chunk in llm.stream(...):
```
This:

- Streams token by token

- Returns partial chunks

- Good for chat UIs

Like ChatGPT typing live.

7️⃣ Understanding ```response```
```
print(response.content)
```
response is NOT just a string.

It is a LangChain message object.

Structure:
```
response.content      # actual text
response.response_metadata
response.id
response.type
```
8️⃣ Understanding response_metadata

You printed:
```
response.response_metadata
```
Output:
```
{
 'model': 'uncensored',
 'created_at': '2025-10-22T12:16:31.9173234Z',
 'done': True,
 'done_reason': 'stop',
 'total_duration': 2630496300,
 'load_duration': 2149414600,
 'prompt_eval_count': 100,
 'prompt_eval_duration': 17446400,
 'eval_count': 140,
 'eval_duration': 397223200,
 'model_name': 'uncensored',
 'model_provider': 'ollama'
}
```
Let’s decode it.

🔹 ```model```

Model name used.

🔹 ```created_at```

Timestamp.

🔹 ```done```

Whether generation completed successfully.

🔹 ```done_reason```

Why it stopped:

- ```stop``` → reached natural stop

- ```length``` → hit max tokens

- ```error``` → failure

🔹 ```total_duration```

Total processing time (in nanoseconds).

Convert:
```
2630496300 ns ≈ 2.63 seconds
```
🔹 ```load_duration```

Time to load model into memory.

Usually high on first call.
Lower after warm-up.

🔹 ```prompt_eval_count```

Number of tokens in prompt.

🔹 ```eval_count```

Number of tokens generated.

🔹 ```eval_duration```

Time spent generating tokens.

🔹 ```model_provider```

Which backend handled it → "ollama"

9️⃣ What’s Actually Happening Internally?

When you call:
```
llm.invoke(prompt)
```
Here’s what happens:

1. LangChain formats message into chat format

2. Sends HTTP request to:
```
http://localhost:11434/api/chat
```
3. Ollama loads model (if not loaded)

4. Model tokenizes prompt

5. Model predicts next tokens one by one

6. Ollama sends response back

7. LangChain wraps it into a message object

🔥 Big Picture Architecture
```
Your Python Code
        ↓
LangChain
        ↓
ChatOllama Wrapper
        ↓
Ollama REST API
        ↓
Local LLM Model
```
🧠 Why Use LangChain Instead of Direct Ollama?

Without LangChain:
```
import requests
```
You’d manually handle:

- Prompt formatting

- Streaming

- Memory

- Tool calls

- Chains

With LangChain you can later:

- Add memory

- Add RAG

- Add tools

- Create agents

- Add structured outputs

- Add function calling