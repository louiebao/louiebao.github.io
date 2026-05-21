# Building a simple AI agent with Python, Ollama and SQLite

I have been hearing about AI agents for some months now. Initially, it was just another AI concept that quickly got lost in the sea of AI jargon. But ever since I started using a coding agent to put together an Android mobile app, my perception changed. What would normally take me at least a year to build part time using a completely new tech stack took a coding agent only a couple of weeks to achieve. An AI agent is no longer a mere concept from a faraway land, it is real, it is tangible and it gives back the most precious resource in the world: time!

Months later, I am no longer content with interacting with coding agents in the CLI. I started to wonder what it actually takes to put together an agent myself, like a “Hello World” app, but more functional than a simple chatbot.

**Objectives:**
- Free
- Lightweight
- Fast to build
- No API keys, no accounts, no containers
- Runs locally and does not connect to any external services

After a bit of tinkering, I got one working, and in doing so I learned something I would never have learned had I not embarked on this journey. Below, I will share the steps for anyone, especially non-technical people to set up an AI agent themselves. Of course, the very first prototype is a long way from a real production grade agent, but the fun is real.

I wanted to mimic the experience of a new user, which is why I chose to build the agent using an old spare Mac. Therefore, the steps are intentionally beginner friendly. There is only one caveat: you will need to have Homebrew installed on your Mac first. If not, that will be your first challenge, go on, get it done, and then come back here.

## Step 1 – Install required software
Use Homebrew to install Python and Ollama. I hope I don't need to explain why you'd need Python. Let's just say it is the coding/scripting language favoured by AI. Also, for an AI agent to work, it will need to talk to an AI model (aka LLM) and Ollama does just that. Remember, our goal is to build a simple agent quickly for learning purposes and not to become an AI infrastructure engineer.

```bash
brew install python ollama
```

Check version:

```bash
python3 --version
ollama --version
```

Create a virtual environment for python:

```bash
python3 -m venv venv
source venv/bin/activate
```

Install a python package that we'll need later:

```
pip3 install langchain-ollama
```

## Step 2 – Download an AI model using Ollama
We've installed Ollama in the previous step, but it is just a soulless shell. It is time to inject it with intelligence. For that, we need to decide on an AI model that we will use for this exercise. Any will do, but given that I have a pretty old Mac, I opted for a lightweight model.

In one terminal, run and keep this local ollama server running in the background:

```bash
ollama serve
```

Open a new terminal, run:

```bash
ollama pull phi3:mini
```

## Step 3 – Create a local database
We are data engineers right? Of course we will need a database. SQLite was chosen as it is a lightweight, file-based database that comes with Python, so no additional installation is required.

Copy and paste the python script below and save as setup_db.py:

```python
import sqlite3

print("Connecting to db...")
conn = sqlite3.connect("sales.db")

cursor = conn.cursor()

cursor.execute("""drop table if exists sales;""")
cursor.execute("""
create table sales 
(
      id          integer   not null primary key
    , customer    text      not null unique
    , amount      real      not null
)
""")

data = [
      ("Alice",   120)
    , ("Bob",      90)
    , ("Charlie", 150)
    , ("Derek",   300)
]

print("Inserting into sales...")
cursor.executemany("insert into sales (customer, amount) values (?, ?)", data)

conn.commit()
conn.close()

print("[sales] database created with sample data.")
```

Run the script to setup the database:

```bash
python3 setup_db.py
```

## Step 4 – Create the agent
Finally, this is what we set out to do: building an AI agent. The magic happens in python where all the ingredients are mixed together. To make things more interesting, we want something more useful than a chatbot. How about using the agent to turn natural language into an SQL query and execute it against the database? How’s that for a data engineer's first AI agent?

Copy and paste the python script below and save as agent.py:

```python
# Remember we installed this package using pip3?
from langchain_ollama import ChatOllama
import sqlite3

# Remember we downloaded this model using Ollama?
llm = ChatOllama(model="phi3:mini", temperature=0)

def clean_sql(text):
    text = text.strip()
    start = text.lower().find("select")
    if start != -1:
        text = text[start:]
    text = text.split(";")[0] + ";"
    return text

def run_sql(query):
    # Remember we created this database earlier?
    conn = sqlite3.connect("sales.db")
    cur = conn.cursor()
    try:
        cur.execute(query)
        return cur.fetchall()
    finally:
        conn.close()

while True:
    q = input("\nAsk: ")
    if q.lower() == "exit":
        break

    prompt = f"You are a SQL generator. Table: sales(id, customer, amount). Question: {q}"
    sql = clean_sql(llm.invoke(prompt).content.strip())

    print("\nSQL:\n", sql)

    try:
        result = run_sql(sql)
        print("\nResult:\n", result)
    except Exception as e:
        print("Error:", e)
```

## Step 5 – Run the agent
Let the fun begin:

```bash
python3 agent.py
```

Try asking:
> Ask: what is the total sales

To exit:
> Ask: exit

The core loop: user asks a question in plain English -> agent sends it to Ollama(phi3:mini) as a SQL-generation prompt -> LLM returns a SQL string -> agent strips noise -> agent executes it against sales.db -> agent prints results.

## Summary
Building an AI agent is not as hard as it may sound. I've demonstrated in 5 steps above that anyone can put together a working agent in 10 minutes. Hope this post sparked your interest to try it yourself.

## A note for Windows users
The code and Ollama commands are cross-platform, so most steps stay the same. The changes are all in the shell commands:

| Mac (Homebrew)                | Windows (PowerShell)                          |
|-------------------------------|-----------------------------------------------|
| `brew install python ollama`  | `winget install Python.Python.3 Ollama.Ollama`|
| `source venv/bin/activate`    | `.\venv\Scripts\Activate.ps1`                 |
| `python3`                     | `python`                                      |
| `pip3`                        | `pip`                                         |
