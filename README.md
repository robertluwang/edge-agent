# Edge Agent

An autonomous AI edge worker designed specifically for resource-constrained environments.

## Solution Background

Running autonomous AI agents locally on edge devices offers great benefits for privacy, local file access, and continuous automation. However, true edge nodes—such as iPhones (via a-Shell/iSH), iPads, WSL environments, and free-tier "tiny VMs" (1GB RAM)—lack the memory and computational power to run modern Large Language Models (LLMs) locally.

**The Solution:** This Edge Agent splits the architecture. 
1. **Local Execution:** The agent's logic, planning, tool execution, and state management run directly on the edge node, maintaining local context and executing local system commands.
2. **Remote Inference:** The heavy LLM inference is offloaded to a secure, remote API gateway (like [LiteLLM Gateway](https://github.com/robertluwang/litellm-gateway)). 
3. **Secure Tunneling:** Because mobile edge nodes (like smartphones or roaming laptops) have dynamic IPs and cannot easily be whitelisted by remote servers, the agent utilizes an outbound SSH tunnel. This creates a secure, encrypted bridge to the gateway without requiring a public IP on the edge node.

## Implementation Details
- **Minimal Footprint:** Written in Python with minimal dependencies to ensure it can run in lightweight environments like `iSH` on iOS or a 1GB RAM Linux VM.

## Key Features

- **Autonomous ReAct Loop:** Employs a robust reasoning-and-acting (ReAct) loop that continuously analyzes tasks, decides on the next action, executes tools, and evaluates the output up to 10 autonomous steps.
- **Local Tool Access:** Safely executes commands, reads files, and writes files directly on the edge node using native Python subprocesses and OS utilities.
- **Built-in Safety Measures:** Includes command blacklisting to prevent destructive operations (like `rm -rf /` or `mkfs`) while running autonomously.
- **Rich CLI Output:** Utilizes the Python `rich` library to provide a beautifully formatted, color-coded terminal interface showing agent thoughts, tool execution, and results in real-time.
- **Provider Agnostic:** By relying on the LiteLLM standard, the agent expects an OpenAI-compatible endpoint (`http://127.0.0.1:4000/v1`), meaning the remote gateway can be swapped between Vertex AI, OpenAI, Anthropic, or local open-source models without changing the edge agent's code.
- **Persistent SSH Tunneling:** The environment configuration automatically checks for and establishes a background SSH tunnel (`ssh -f -N -L`) to the inference server, ensuring uninterrupted access to the LLM backend even as networks change.

## Setup

```bash
pip install -r requirements.txt

# 1. Copy the edge node template to your home directory
cp .env.edge_node_template ~/.env

# 2. Edit ~/.env with your keys and VM IP
nano ~/.env

# 3. Add this to your ~/.bashrc so it runs automatically
echo "source ~/.env" >> ~/.bashrc
source ~/.bashrc
```

## Usage & Example

Run the script and provide your task as an argument. The agent will autonomously break down the goal, use local tools to investigate the environment, and perform actions.

```bash
python3 edge_agent.py "Write a simple python script in hello.py that prints 'hello world', then execute it and tell me the output."
```

**Example Output Flow:**
1. **Agent Thoughts:** The agent decides it needs to write the file first.
2. **Tool Call:** It outputs a JSON block calling `write_file` with the path `hello.py` and the required code.
3. **Tool Output:** The local system saves the file and returns success.
4. **Agent Thoughts:** The agent sees the success and decides to run the script.
5. **Tool Call:** It outputs a JSON block calling `run_command` with `python3 hello.py`.
6. **Tool Output:** The local system returns `hello world`.
7. **Final Answer:** The agent recognizes the goal is complete and outputs a final summary confirming the output.