<div align="center">

# 👁️ AgentWatch

### Open Source Observability for AI Agents

**Stop flying blind. See what your AI agents are doing.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Twitter Follow](https://img.shields.io/twitter/follow/maxcodesai?style=social)](https://x.com/maxcodesai)

[Quick Start](#-quick-start) • [Features](#-features) • [Examples](#-examples) • [Dashboard](#-dashboard) • [Integrations](#-integrations)

<img src="https://via.placeholder.com/800x400/667eea/ffffff?text=AgentWatch+Dashboard+Preview" alt="AgentWatch Dashboard" width="800"/>

</div>

---

## 🎯 The Problem

You're building AI agents with GPT-4, Claude, or local models. They work... but you have **no idea** what's happening:

- 💸 **"Why is my API bill $5,000?"** - No cost tracking
- 🐌 **"Why is this so slow?"** - No performance metrics  
- 💥 **"Why did it fail?"** - No error monitoring
- 📊 **"Which agent is expensive?"** - No analytics

**You're flying blind.**

---

## ✨ The Solution

**AgentWatch** - One decorator. Complete visibility.

```python
from agentwatch import watch

@watch.agent(name="my-agent")
def my_ai_function(prompt: str):
    response = openai.ChatCompletion.create(...)
    return response

# That's it! Now you see everything:
# ✅ Every call logged
# ✅ Cost tracked in real-time
# ✅ Performance monitored
# ✅ Errors caught
# ✅ Beautiful dashboard
```

---

## 🚀 Quick Start

### Installation

```bash
pip install agentwatch
```

### Basic Usage

```python
from agentwatch import watch

@watch.agent(name="email-bot", tags=["production"])
def send_email(to: str, subject: str):
    # Your AI logic here
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=[{"role": "user", "content": f"Write email to {to} about {subject}"}]
    )
    return response.choices[0].message.content

# Use it normally
result = send_email("user@example.com", "Meeting Tomorrow")

# Every call is now tracked!
```

### View Dashboard

```bash
agentwatch dashboard
```

Open **http://localhost:3000** and see:

- 📊 Total calls, costs, errors
- ⚡ Performance metrics per agent
- 📈 Real-time activity feed
- 🎯 Cost breakdown by agent

---

## 🎨 Features

### 🔍 **Complete Visibility**
Track every agent call with input, output, duration, cost, and status.

### 💰 **Cost Tracking**
See exactly how much each agent costs. No more surprise bills.

### ⚡ **Performance Monitoring**
Identify slow agents. Optimize what matters.

### 🐛 **Error Tracking**
Catch failures instantly. See error rates per agent.

### 📊 **Beautiful Dashboard**
Real-time web UI with charts, stats, and activity feed.

### 🗄️ **Local Storage**
Everything stored in SQLite. No cloud required. Your data stays yours.

### 🏷️ **Tags & Filtering**
Organize agents by environment, team, or purpose.

### 📤 **Export Data**
Export to CSV/JSON for analysis in Excel, Python, or BI tools.

### 🚀 **Zero Config**
Works out of the box. No setup, no API keys, no hassle.

### 🪶 **Lightweight**
< 1ms overhead per call. Won't slow down your agents.

---

## 📸 Dashboard Preview

<div align="center">

### Overview Stats
<img src="https://via.placeholder.com/700x200/667eea/ffffff?text=Total+Calls+%7C+Cost+%7C+Errors+%7C+Avg+Duration" alt="Stats" width="700"/>

### Per-Agent Metrics
<img src="https://via.placeholder.com/700x250/764ba2/ffffff?text=Agent+Cards+with+Calls%2C+Cost%2C+Duration%2C+Errors" alt="Agents" width="700"/>

### Recent Activity
<img src="https://via.placeholder.com/700x300/667eea/ffffff?text=Real-time+Call+Log+with+Status%2C+Duration%2C+Cost" alt="Activity" width="700"/>

</div>

---

## 💡 Examples

### OpenAI Integration

```python
from agentwatch import watch
from openai import OpenAI

client = OpenAI()

@watch.agent(name="gpt-assistant", tags=["openai", "production"])
def ask_gpt(prompt: str):
    response = client.chat.completions.create(
        model="gpt-4",
        messages=[{"role": "user", "content": prompt}]
    )
    
    # Calculate cost
    tokens = response.usage.total_tokens
    cost = (tokens / 1000) * 0.03  # GPT-4 pricing
    
    return {
        "answer": response.choices[0].message.content,
        "tokens": tokens,
        "cost": cost
    }

# Use it
result = ask_gpt("Explain quantum computing")
print(f"Answer: {result['answer']}")
print(f"Cost: ${result['cost']:.4f}")

# Check total costs
stats = watch.stats(agent_name="gpt-assistant")
print(f"Total spent: ${stats['total_cost']:.2f}")
```

### Multiple Agents

```python
from agentwatch import watch

@watch.agent(name="email-bot", tags=["production"])
def send_email(to: str, subject: str):
    # Email logic
    return {"status": "sent"}

@watch.agent(name="slack-bot", tags=["production"])
def send_slack(channel: str, message: str):
    # Slack logic
    return {"status": "sent"}

@watch.agent(name="data-processor", tags=["etl"])
def process_data(data: list):
    # Processing logic
    return {"processed": len(data)}

# Use them
send_email("user@example.com", "Hello")
send_slack("#general", "Deployment complete")
process_data([1, 2, 3, 4, 5])

# See stats for all agents
stats = watch.stats()
print(f"Total agents: {stats['total_agents']}")
print(f"Total calls: {stats['total_calls']}")
print(f"Total cost: ${stats['total_cost']:.2f}")
```

### Error Tracking

```python
from agentwatch import watch

@watch.agent(name="risky-agent", tags=["experimental"])
def risky_operation(data: dict):
    if not data.get("valid"):
        raise ValueError("Invalid data")
    return {"result": "success"}

# Errors are automatically tracked
try:
    risky_operation({"valid": False})
except ValueError:
    pass

# Check error rate
stats = watch.stats(agent_name="risky-agent")
print(f"Error rate: {stats['error_rate']*100:.1f}%")
```

### Manual Tracking

For more control:

```python
from agentwatch import watch

# Start tracking
call_id = watch.start(
    agent_name="custom-agent",
    input_data={"prompt": "Hello"}
)

# Your logic
result = do_something()

# End tracking
watch.end(
    call_id=call_id,
    output_data={"result": result},
    cost=0.002,
    error=None  # or error message if failed
)
```

---

## 🎛️ Dashboard

### Start Dashboard

```bash
# Default port (3000)
agentwatch dashboard

# Custom port
agentwatch dashboard --port 8080

# Custom database
agentwatch dashboard --db /path/to/custom.db
```

### Features

- **📊 Overview Stats** - Total calls, costs, errors at a glance
- **🤖 Agent Cards** - Per-agent metrics with drill-down
- **📞 Activity Feed** - Real-time call log with filtering
- **🔄 Auto-refresh** - Updates every 5 seconds
- **🎨 Beautiful UI** - Clean, modern design

---

## 🔧 CLI Commands

```bash
# View statistics
agentwatch stats

# Filter by agent
agentwatch stats --agent my-agent

# List all agents
agentwatch list

# Export data
agentwatch export data.csv
agentwatch export data.json --format json

# Start dashboard
agentwatch dashboard --port 3000
```

---

## 🔌 Integrations

### Kiro IDE

Monitor your Kiro AI assistant:

```python
from agentwatch import watch

@watch.agent(name="kiro-assistant", tags=["kiro", "production"])
def ask_kiro(prompt: str):
    response = kiro.process(prompt)
    return response

# Track: token usage, costs, response times, errors
result = ask_kiro("How do I use decorators?")
```

See [`examples/kiro_integration.py`](examples/kiro_integration.py) and [`docs/KIRO_INTEGRATION.md`](docs/KIRO_INTEGRATION.md)

### LangChain (Coming Soon)

```python
from agentwatch import watch
from langchain import OpenAI

@watch.agent(name="langchain-agent")
def langchain_call(prompt: str):
    llm = OpenAI()
    return llm(prompt)
```

### LlamaIndex (Coming Soon)

### AutoGPT (Coming Soon)

### CrewAI (Coming Soon)

---

## 📚 Documentation

- [Quick Start Guide](QUICKSTART.md) - Get started in 5 minutes
- [Kiro Integration](docs/KIRO_INTEGRATION.md) - Monitor Kiro AI assistant
- [Examples](examples/) - Real-world usage examples
- [Contributing](CONTRIBUTING.md) - How to contribute
- [Changelog](CHANGELOG.md) - Version history
- [Roadmap](TODO.md) - What's coming next

---

## 🎯 Use Cases

### 1. **Cost Control**
Track API costs in real-time. Set budgets. Avoid surprise bills.

### 2. **Performance Optimization**
Find slow agents. Optimize bottlenecks. Improve user experience.

### 3. **Error Monitoring**
Catch failures instantly. Debug faster. Improve reliability.

### 4. **Usage Analytics**
Understand how agents are used. Make data-driven decisions.

### 5. **Development**
Debug agents during development. See what's happening under the hood.

### 6. **Production Monitoring**
Monitor agents in production. Get alerted on issues.

---

## 🏆 Why AgentWatch?

### vs. Manual Logging
- ❌ Manual: Write logging code everywhere
- ✅ AgentWatch: One decorator

### vs. Cloud Services
- ❌ Cloud: Send data to third parties, pay monthly
- ✅ AgentWatch: Local storage, free forever

### vs. Building Your Own
- ❌ DIY: Weeks of development, maintenance burden
- ✅ AgentWatch: Install in 30 seconds, works out of the box

### vs. Nothing
- ❌ Nothing: Flying blind, surprise bills, no debugging
- ✅ AgentWatch: Complete visibility, cost control, easy debugging

---

## 🚦 Getting Started

### 1. Install

```bash
pip install agentwatch
```

### 2. Add Decorator

```python
from agentwatch import watch

@watch.agent(name="my-agent")
def my_function():
    return "result"
```

### 3. View Dashboard

```bash
agentwatch dashboard
```

### 4. Open Browser

http://localhost:3000

**That's it!** 🎉

---

## 🤝 Contributing

We love contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Areas We Need Help

- 🔌 More LLM integrations (Anthropic, Cohere, etc.)
- 💰 Better cost calculation algorithms
- 🎨 Dashboard improvements
- 📊 Advanced analytics features
- 📖 Documentation improvements
- 🌍 Internationalization

### Contributors

<a href="https://github.com/sh1esty1769/AI-for-observability/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=sh1esty1769/AI-for-observability" />
</a>

---

## 🗺️ Roadmap

### v0.2.0 - Cost Calculation
- [ ] Automatic cost calculation for OpenAI
- [ ] Anthropic Claude cost tracking
- [ ] Cohere cost tracking
- [ ] Custom cost functions

### v0.3.0 - Integrations
- [ ] LangChain integration
- [ ] LlamaIndex integration
- [ ] AutoGPT integration
- [ ] CrewAI integration

### v0.4.0 - Dashboard++
- [ ] Advanced filtering
- [ ] Charts and graphs
- [ ] Dark mode
- [ ] Export from dashboard

### v0.5.0 - Alerts
- [ ] Cost threshold alerts
- [ ] Error rate alerts
- [ ] Webhook notifications
- [ ] Email notifications

### v1.0.0 - Production Ready
- [ ] Multi-database support (PostgreSQL, MySQL)
- [ ] Team collaboration features
- [ ] API for external tools
- [ ] Enterprise features

See [TODO.md](TODO.md) for full roadmap.

---

## 📊 Stats

<div align="center">

![GitHub stars](https://img.shields.io/github/stars/sh1esty1769/AI-for-observability?style=social)
![GitHub forks](https://img.shields.io/github/forks/sh1esty1769/AI-for-observability?style=social)
![GitHub watchers](https://img.shields.io/github/watchers/sh1esty1769/AI-for-observability?style=social)

![GitHub issues](https://img.shields.io/github/issues/sh1esty1769/AI-for-observability)
![GitHub pull requests](https://img.shields.io/github/issues-pr/sh1esty1769/AI-for-observability)
![GitHub last commit](https://img.shields.io/github/last-commit/sh1esty1769/AI-for-observability)

</div>

---

## 💬 Community

- **GitHub Discussions:** [Ask questions, share ideas](https://github.com/sh1esty1769/AI-for-observability/discussions)
- **Issues:** [Report bugs, request features](https://github.com/sh1esty1769/AI-for-observability/issues)
- **Twitter/X:** [@maxcodesai](https://x.com/maxcodesai)

---

## 📄 License

MIT License - see [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

Built with ❤️ by developers who were tired of flying blind.

Inspired by:
- [Sentry](https://sentry.io/) - Error tracking done right
- [Datadog](https://www.datadoghq.com/) - Observability platform
- [LangSmith](https://www.langchain.com/langsmith) - LLM observability

---

## 🔗 Links

- **GitHub:** https://github.com/sh1esty1769/AI-for-observability
- **Issues:** https://github.com/sh1esty1769/AI-for-observability/issues
- **Discussions:** https://github.com/sh1esty1769/AI-for-observability/discussions
- **Twitter/X:** [@maxcodesai](https://x.com/maxcodesai)

---

## ⭐ Star History

<div align="center">

[![Star History Chart](https://api.star-history.com/svg?repos=sh1esty1769/AI-for-observability&type=Date)](https://star-history.com/#sh1esty1769/AI-for-observability&Date)

</div>

---

<div align="center">

### **Stop flying blind. Start watching your agents.** 👁️

**[Get Started Now](#-quick-start)** • **[View Examples](#-examples)** • **[Read Docs](#-documentation)**

<br/>

**If AgentWatch helps you, give us a ⭐ on GitHub!**

[![GitHub stars](https://img.shields.io/github/stars/sh1esty1769/AI-for-observability.svg?style=social&label=Star)](https://github.com/sh1esty1769/AI-for-observability)

<br/>

Made with 💜 by [@maxcodesai](https://x.com/maxcodesai)

</div>
