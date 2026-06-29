# Edge Agent

An autonomous AI edge worker running locally on a secure node.

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
