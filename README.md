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

## Usage

```bash
python3 edge_agent.py "<task description>"
```