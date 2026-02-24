🚀 Welcome to CS50x Scratch Intro

1) Big Picture
 - Artificial intelligence (AI) is bringing new excitement to computer science and beyond.
 - Knowing fundamentals lets you be the pilot, not just a passenger, even when AI removes bottlenecks.
 - Pair your CS foundations with AI to unlock more opportunities for you and the people you serve.

2) Tools 🛠️
 - Visual Studio Code (VS Code) is an IDE where you create and organize code.
 - We’ll sketch a tiny chatbot, `chat.py`, to see what’s coming.

3) First Chatbot Pass (hard‑coded prompt) 🧠

```python
from openai import OpenAI

client = OpenAI()

response = client.responses.create(
    input="In one sentence, what is CS50?",
    model="gpt-5"
)

print(response.output_text)
```

Output (example):
```
CS50 is Harvard's introduction to computer science.
```

💡 Notes: import the OpenAI library, make a client, ask a question via `input=...`, and print the reply.

4) Let the user ask the question 💬

```python
from openai import OpenAI

client = OpenAI()

prompt = input("Prompt: ")

response = client.responses.create(
    input=prompt,
    model="gpt-5"
)

print(response.output_text)
```

Output (example, if prompt was "What is Python?"):
```
Python is a popular programming language.
```

💡 Notes: add `prompt` so the user can ask anything.

5) Add a system prompt for tone/context 🎭

```python
from openai import OpenAI

client = OpenAI()

user_prompt = input("Prompt: ")
system_prompt = "Limit your answer to one sentence. Pretend you're a cat."

response = client.responses.create(
    input=user_prompt,
    instructions=system_prompt,
    model="gpt-5"
)

print(response.output_text)
```

Output (example, prompt "What is CS50?", cat mode):
```
CS50 is Harvard's intro to computer science, meow.
```

💡 Notes: `instructions` (system prompt) steer style/limits while the user supplies the question.

6) Takeaway 🌟
 - In ~10 lines you can build a surprisingly powerful program.
 - CS50 Duck is your built‑in rubber duck for this course.
