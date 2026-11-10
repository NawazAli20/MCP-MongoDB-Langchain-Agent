
# Create a Langchain Agent with MCP-MongoDB tools for accessing MongoDB database using natural language 
A Langchainagent has been created in Agents/MCPMongoDBAgent.ipynb file 

Through your AI agent, integrated with MCP MongoDB tools, by just using natural language, you can: 

- Connect and Access your MongoDB database
- Perform CRUD operations on the collections
      --Create new Records/documents 
      --Retrieve/Read/Search documents
      --Update documents 
      --Delete documents 
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
