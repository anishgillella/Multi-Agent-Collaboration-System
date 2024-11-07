
# Multi-Agent Collaboration System

This project demonstrates a multi-agent collaboration workflow using LangChain, LangGraph, and OpenAI's GPT models. The workflow consists of agents performing specialized tasks such as data retrieval and chart generation, coordinated through a state graph that manages task transitions.

## Overview

The system uses two main agents:
- **Research Agent**: Gathers data from external sources (e.g., Tavily API for structured data searches).
- **Chart Generator Agent**: Generates charts based on retrieved data, using a local Python REPL tool.

Each agent is connected to the others through a structured workflow in which task transitions are managed by a central graph (`StateGraph`), allowing tasks to pass seamlessly from one agent to another.

## Key Features

- **Agent Collaboration**: Agents with specialized tools work together to fulfill complex tasks.
- **State Graph for Task Flow**: A `StateGraph` manages task routing and allows for conditional transitions between agents.
- **Dynamic Code Execution**: A Python REPL allows the system to run Python code dynamically, generating real-time visualizations or analysis.

## Setup and Installation

### Prerequisites

- Python 3.8 or higher
- Jupyter Notebook (optional for running in an interactive environment)

### Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/anishgillella/Multi-Agent-Collaboration-System.git
   cd Multi-Agent-Collaboration-System
   ```

2. Install the required packages:
   ```python
   %pip install langchain langgraph langchain_openai langchain_experimental langchain_core langsmith pandas matplotlib
   ```

3. Set up API keys for OpenAI and Tavily:
   ```python
   import os
   import getpass

   def set_if_undefined(var: str):
       if var not in os.environ:
           os.environ[var] = getpass.getpass(f"Please enter the {var} key: ")

   set_if_undefined("OPENAI_API_KEY")
   set_if_undefined("TAVILY_API_KEY")

   os.environ["LANGCHAIN_TRACING_V2"] = "true"
   os.environ["LANGCHAIN_API_KEY"] = "your_langchain_api_key"
   os.environ["LANGCHAIN_PROJECT"] = "Multi Agent Collaboration"
   ```

## Project Structure

### Agent Creation

1. **Research Agent**: Uses the Tavily tool for data search and retrieval.
2. **Chart Generator Agent**: Uses a Python REPL tool to execute code and generate charts based on retrieved data.

Each agent is created with `create_agent`, a function that sets up an agent’s behavior and tools.

### Tool Definition

- **Tavily Search Tool**: Retrieves structured data using Tavily's search API.
- **Python REPL Tool**: Executes Python code for data visualization or analysis.

### Graph Definition

The system workflow is defined using a `StateGraph`:

1. **Nodes**: Each agent (`Researcher`, `chart_generator`) and `ToolNode` (for handling tools) is added as a node in the `StateGraph`.
2. **Edge Logic**: `router` logic routes tasks to the appropriate agent or tool based on the agent’s output.
3. **Start and End Points**: The graph starts with the `Researcher` node and completes once a final answer is reached.

## Usage

Run the workflow as follows:

1. **Define the Task**: Specify the task for the agents to perform, such as fetching data and generating a chart:
   ```python
   events = graph.stream(
       {
           "messages": [
               HumanMessage(
                   content="Fetch the UK's GDP over the past 5 years, then draw a line graph of it. Once you code it up, finish."
               )
           ],
       },
       {"recursion_limit": 150},
   )
   ```

2. **Execute the Workflow**:
   ```python
   for s in events:
       print(s)
       print("----")
   ```

3. **Visualize the Workflow (Optional)**:
   Use `display()` to visualize the task flow:
   ```python
   from IPython.display import display, Image

   try:
       display(Image(graph.get_graph(xray=True).draw_mermaid_png()))
   except Exception:
       pass
   ```

## Example Output

An example output might look like:
```
{'Researcher': {'messages': [HumanMessage(content='Retrieved data on UK GDP.', name='Researcher')]}}
----
{'chart_generator': {'messages': [HumanMessage(content='Generated a line graph for UK GDP.', name='chart_generator')]}}
----
{'supervisor': {'next': 'FINISH'}}
----
```

## Contributing

Contributions are welcome! Fork the repository, create a new branch, and submit a pull request.
