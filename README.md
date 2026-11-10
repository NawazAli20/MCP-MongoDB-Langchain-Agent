
# Create a Langchain Agent with MCP-MongoDB tools for accessing MongoDB database using natural language 
A Langchainagent has been created in Agents/MCPMongoDBAgent.ipynb file 

Through your AI agent, integrated with MCP MongoDB tools, by just using natural language, you can: 

- Connect and Access your MongoDB database
- Perform CRUD operations on the collections
      - Create new Records/documents 
      - Retrieve/Read/Search documents
      - Update documents 
      - Delete documents 
- Perform operations on database and collection for creations and modifications as well


### How to run 

uv run MCPMongoDBAgent.py .py (for .py file)

run all cells in jupyter notebook (for .ipynb file)


# Repo and Dependencies Installations commands 

- Instal [nodeJS](https://nodejs.org/en/download) 
- Issue: uv add langchain-mcp-adapters

#Checking for Node and npx (in the terminal): 
node -v 
npx -v

MongoDB Installations: https://www.mongodb.com/docs/manual/administration/install-community/?operating-system=macos&macos-installation-method=homebrew

MongoDB CRUD operations syntax: https://www.mongodb.com/docs/manual/crud/?msockid=050612d8208b6ac13bc3054421d26b1d
MogoDB MCP: 

MCP Documentations: https://www.mongodb.com/docs/mcp-server/get-started/?msockid=050612d8208b6ac13bc3054421d26b1d

Manually Running mongodb community@9.0 (After tar ball extraction in the current working directory)

mkdir -p "$HOME/mongodb-data-9.0" &&
./mongodb-macos-arm64-9.0.4/bin/mongod \
  --dbpath "$HOME/mongodb-data-9.0" \
  --bind_ip 127.0.0.1 \
  --port 27018


# MongoDB CRUD with a LangChain MCP Agent

Run this code in a local VS Code Jupyter notebook. You will run a provided natural-language count query, then write four prompts that create, read, update, and delete a fictional student record. Use Compass to verify what the agent actually did.

## 1. Install MongoDB Community Edition and Compass

**macOS.** With Homebrew installed, open Terminal and run:

~~~bash
brew tap mongodb/brew
brew install mongodb-community@8.0
brew services start mongodb-community@8.0
brew services list
~~~

Download the Compass DMG for Apple silicon or Intel as appropriate. Open the DMG and drag Compass into Applications.

**Windows.** Download the MongoDB Community Server 8.0 MSI. Choose **Complete**, keep **Install MongoD as a Service** enabled, and select **Install MongoDB Compass**. If Compass was skipped, download and install it separately. In Windows **Services**, verify that the MongoDB service is running.

- [MogoDB](https://www.mongodb.com/try/download/community)
- [Compass](https://www.mongodb.com/try/download/compass)

**Linux.** Follow the installation guide for your distribution, start the MongoDB service, and download the matching Compass package.

- [Community Server download](https://www.mongodb.com/try/download/community)
- [macOS guide](https://www.mongodb.com/docs/v8.0/tutorial/install-mongodb-on-os-x/)
- [Windows guide](https://www.mongodb.com/docs/v8.0/tutorial/install-mongodb-on-windows/)
- [Linux guide](https://www.mongodb.com/docs/v8.0/administration/install-on-linux/)
- [Compass download](https://www.mongodb.com/try/download/compass)

## 2. Create the practice collection

1. Open Compass and connect to `mongodb://127.0.0.1:27017`. Use your local instance and fictional data for this lab.
2. Select **Create Database**. Enter `university` for the database and `students` for the collection. Use these exact lowercase names throughout.
3. Open **Documents → Add Data → Insert Document** in the collection. Switch to JSON view and insert the following array once. Refresh the collection and check that there are three documents.

~~~json
[
  {"_id": "S101", "name": "James Cercone", "dept_name": "Computer Science", "gpa": 3.62, "credit_hours": 94},
  {"_id": "S102", "name": "Maya Chen", "dept_name": "Electrical Engineering", "gpa": 3.71, "credit_hours": 78},
  {"_id": "S103", "name": "Jordan Brooks", "dept_name": "Biology", "gpa": 3.45, "credit_hours": 61}
]
~~~

## 3. Set up the notebook

Install [Node.js LTS](https://nodejs.org/en/download) and verify `node --version` and `npx --version` in a fresh terminal. From your course uv project, run the command below. In a new folder without a project, run `uv init` first. Select the project’s `.venv` interpreter as your VS Code notebook kernel.

~~~bash
uv add langchain langchain-groq langchain-mcp-adapters python-dotenv ipykernel
~~~

Create a `.env` file beside the notebook and replace the Groq key placeholder. Keep the file out of your submission and version control.

~~~dotenv
MONGODB_URI=mongodb://127.0.0.1:27017
GROQ_API_KEY=replace_with_your_key
~~~

## 4. Run the starter count query

Run these as separate notebook cells. LangChain sends tool requests through the MCP client to the MongoDB MCP server launched by `npx`. Compass connects to the same database independently.

~~~python
import os

from dotenv import load_dotenv
from langchain.agents import create_agent
from langchain.chat_models import init_chat_model
from langchain_mcp_adapters.client import MultiServerMCPClient

load_dotenv()

model = init_chat_model(
    model="openai/gpt-oss-120b",
    model_provider="groq",
)

client = MultiServerMCPClient({
    "mongodb": {
        "transport": "stdio",
        "command": "npx",
        "args": ["-y", "mongodb-mcp-server@latest", "--loggers", "stderr"],
        "env": {"MDB_MCP_CONNECTION_STRING": os.environ["MONGODB_URI"]},
    }
})

mongo_tools = await client.get_tools()
print("Discovered tools:", sorted(tool.name for tool in mongo_tools))
~~~

~~~python
tool_names = {"count", "find", "insert-many", "update-many", "delete-many"}
lab_tools = [tool for tool in mongo_tools if tool.name in tool_names]
missing = tool_names - {tool.name for tool in lab_tools}
if missing:
    raise RuntimeError(f"Missing MongoDB tools: {sorted(missing)}")

agent = create_agent(
    model=model,
    tools=lab_tools,
    system_prompt=(
        "Use MongoDB tools only on the university database and students collection. "
        "Perform only the requested operation. For updates and deletes, require "
        "an exact _id filter targeting one document. Report the actual tool result."
    ),
)

async def run_query(query: str) -> None:
    result = await agent.ainvoke(
        {"messages": [{"role": "user", "content": query}]}
    )
    for message in result["messages"]:
        for call in getattr(message, "tool_calls", []):
            print("Tool used:", call["name"])
    print(result["messages"][-1].content)
~~~

~~~python
user_query = (
    "How many documents are in the students collection "
    "in the university database? Use the count tool."
)
await run_query(user_query)
~~~

Confirm that the tool trace shows `count` and the agent reports **3**. Compare with Compass.


Resource Links:
- [Complete youtube playlist](https://www.youtube.com/playlist?list=PLfpB0rPkNb_8)
- [MCP:](https://modelcontextprotocol.io/docs/2026-07-28/getting-started/intro)
- [Official MCP Registry:](https://registry.modelcontextprotocol.io/)
- [MCP github:](https://github.com/modelcontextprotocol)
- [RAG](https://docs.langchain.com/oss/python/deepagents/rag)
- [Langgraph workflows](https://docs.langchain.com/oss/python/langgraph/workflows-agents)
- [arxivLoader](https://reference.langchain.com/python/langchain-community/document_loaders/arxiv/ArxivLoader)
- [arxivAPI](https://info.arxiv.org/help/api/basics.html)
- [Langgraph memory](https://docs.langchain.com/oss/python/langgraph/add-memory#manage-checkpoints)
- [Langgraph docs](https://docs.langchain.com/oss/python/langgraph/overview)
- [Langchain's Pre-build Middlware](https://docs.langchain.com/oss/python/langchain/middleware/built-in)
- [Langchain's middleware](https://docs.langchain.com/oss/python/langchain/middleware/overview)
- [Langgraph's short-term memory](https://docs.langchain.com/oss/python/langchain/short-term-memory) 
- [Langchain Agent](https://docs.langchain.com/oss/python/langchain/agents)
- [DuckDuckGoSearch](https://reference.langchain.com/python/langchain-community/tools/ddg_search/tool/DuckDuckGoSearchRun)
- [Youdotcom API Key](https://you.com/platform/api-keys) 
- [OpenWeatherMap](https://openweathermap.org/)
- [UV github repo](https://github.com/astral-sh/uv)
- [langchain docs]()
- [Groq API Key](https://console.groq.com/keys)
- [OpenAI API Key](https://platform.openai.com/api-keys)

## Install uv On macOS and Linux.
curl -LsSf https://astral.sh/uv/install.sh | sh

## Install uv On Windows.
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# for a fresh repo
### create a project directory and issue the following commands in the project directory:

uv init

uv venv  

source .venv/bin/activate (#activate .venv)

### then install all the dependecies

uv add *packagename*

Examples: 

uv add langchain

uv add langchain-openai 

uv add langchain-groq 

uv add ipykernel

uv add python-dotenv

#### or if you have listed all the required packages in the requirements.txt, issue: 

uv add -r requirements.txt


# Installations for a cloned repo

git clone https://github.com/NawazAli20/LLMsIntro

if you have .toml and/or .lock file just issue

uv sync 
