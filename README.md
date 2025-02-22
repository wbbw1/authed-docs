# 🔐 Authed

<div align="center">

[![PyPI version](https://badge.fury.io/py/agent-auth.svg)](https://badge.fury.io/py/agent-auth)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/FastAPI-005571?style=flat&logo=fastapi)](https://fastapi.tiangolo.com)

**OAuth for AI Agents | Built for the Age of AI**

[Documentation](https://docs.getauthed.dev) • [Quick Start](#⚡-quick-start) • [Examples](#💡-examples) • [Why Authed?](#🤔-why-authed)

</div>

---

## 🤔 Why Authed?

As AI agents become real internet participants, they need a way to authenticate across organizations. Current solutions like OAuth and API keys were built for humans and apps, forcing agents to rely on static credentials that don't scale.

```python
# 😫 Old way: Static API keys
headers = {"Authorization": "Bearer static_api_key_123"}  # Same key everywhere

# 🚀 Authed way: Dynamic agent-to-agent auth
async with auth.secure_request("target-agent") as session:
    response = await session.post("/endpoint")  # Automatic DPoP-based auth
```

## ⚡ Quick Start

```bash
# Install Authed
pip install agent-auth

# Set up your first agent
agent-auth init config
agent-auth agents create --name my-first-agent
```

## 💡 Examples

### Real-World: Healthcare Data Exchange

```python
from fastapi import FastAPI
from langchain.agents import AgentExecutor, Tool
from langchain.chat_models import ChatOpenAI
from agent_auth_client import verify_fastapi, AgentAuthManager

app = FastAPI()
auth = AgentAuthManager.from_env()

# Health Records Agent: Securely manages patient data
class HealthRecordsAgent:
    @app.post("/health-records")
    @verify_fastapi  # 🔒 Secure health data endpoint
    async def process_insurance_request(request: Request):
        # Create a secure agent for processing health records
        llm = ChatOpenAI(temperature=0)
        tools = [
            Tool(
                name="fetch_patient_history",
                func=lambda patient_id: "Retrieve encrypted patient history",
                description="Securely fetch patient medical history"
            ),
            Tool(
                name="prepare_insurance_report",
                func=lambda data: "Generate insurance-ready health report",
                description="Prepare medical data for insurance processing"
            )
        ]
        
        agent = AgentExecutor.from_agent_and_tools(
            agent=your_agent,
            tools=tools
        )
        
        return await agent.arun("Prepare insurance report for patient")

# Insurance Broker Agent: Processes claims
class InsuranceBrokerAgent:
    async def request_health_records(self, patient_id: str):
        # Authenticate and request records from health records agent
        async with auth.secure_request("health-records-agent") as session:
            response = await session.post(
                "http://health-records-agent/health-records",
                json={
                    "patient_id": patient_id,
                    "request_type": "insurance_verification",
                    "required_fields": [
                        "diagnosis_codes",
                        "treatment_history",
                        "prescription_records"
                    ]
                }
            )
        return response.json()

# Example usage
@app.post("/process-claim")
@verify_fastapi
async def process_insurance_claim(request: Request):
    broker = InsuranceBrokerAgent()
    # Securely fetch and process health records
    records = await broker.request_health_records("patient123")
    return {"status": "Claim processed", "verification": records}
```

### Basic FastAPI Integration

```python
from fastapi import FastAPI
from agent_auth_client import verify_fastapi, AgentAuthManager

app = FastAPI()

@app.post("/secure")
@verify_fastapi  # 🔒 One line to secure your endpoint
async def secure_endpoint(request: Request):
    return {"message": "Authenticated!"}
```

### Make Authenticated Requests

```python
# Initialize with environment variables
auth = AgentAuthManager.from_env()

# Make secure requests to other agents
async with auth.secure_request("target-agent") as session:
    response = await session.post(
        "http://other-agent/secure",
        json={"message": "Hello from Agent!"}
    )
```

## 🎯 Core Features

- **🤖 True Agent Identity**: Each agent gets a unique, verifiable ID
- **🔑 DPoP-Based Auth**: Proof of possession, not static keys
- **🎮 Policy Engine**: Dynamic access control between agents
- **⚡ FastAPI Integration**: One-line endpoint protection
- **🛠️ Developer Experience**: Simple SDK and CLI tools

## 🏗️ Use Cases

<table>
<tr>
<td width="33%">
<h3>🤝 Agent Communication</h3>
Secure, authenticated messaging between independent AI agents
</td>
<td width="33%">
<h3>💸 Transaction Systems</h3>
Build secure transaction protocols with built-in identity verification
</td>
<td width="33%">
<h3>🔒 Access Control</h3>
Implement custom access scopes and permission boundaries
</td>
</tr>
</table>

## 🔧 Environment Setup

```bash
# Set your environment variables
export AGENT_AUTH_REGISTRY_URL="your-registry-url"
export AGENT_AUTH_AGENT_ID="your-agent-id"
export AGENT_AUTH_AGENT_SECRET="your-agent-secret"
```

## 📚 Documentation

<table>
<tr>
<td width="25%">
<a href="https://docs.getauthed.dev/what-is-authed">
🎓 Core Concepts
</a>
</td>
<td width="25%">
<a href="https://docs.getauthed.dev/capabilities">
🎯 Use Cases
</a>
</td>
<td width="25%">
<a href="https://docs.getauthed.dev/sdk-guide">
📖 SDK Guide
</a>
</td>
<td width="25%">
<a href="https://docs.getauthed.dev/cli-tools">
💻 CLI Reference
</a>
</td>
</tr>
</table>

## ⚠️ Security Note

Never commit agent secrets or provider credentials to version control. Use environment variables or secure secret management solutions in production.

## 🤝 Contributing

We love contributions! Check out our [Contributing Guide](CONTRIBUTING.md) to get started.

<div align="center">

Made with ❤️ for the AI Agent ecosystem

</div>
