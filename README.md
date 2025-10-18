# 🌪️ Vortex AI

<div align="center">

**A Multi-Agent Trading Simulation Platform**

[![Python](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)
[![Gradio](https://img.shields.io/badge/interface-Gradio-orange.svg)](https://gradio.app/)

*Where AI meets Wall Street - autonomous trading agents making decisions in real-time*

[Features](#-features) • [Quick Start](#-quick-start) • [Architecture](#-architecture) • [Documentation](#-documentation)

</div>

---

## 🎯 What is Vortex AI?

Vortex AI is a sophisticated trading simulation platform that brings together autonomous AI agents, each with distinct investment philosophies inspired by legendary investors. Watch as these agents analyze markets, make trades, and manage portfolios—all while you monitor their every move through an elegant dashboard.

Whether you're researching AI-driven trading strategies, learning about market dynamics, or building your own trading bots, Vortex AI provides the perfect sandbox.

## ✨ Features

### 🤖 **Four Unique Trading Agents**
- **Warren** - Value investor seeking undervalued opportunities
- **George** - Aggressive macro trader capitalizing on market inefficiencies
- **Ray** - Systematic diversification using risk parity principles
- **Cathie** - Innovation-focused trader targeting disruptive technologies

### 📊 **Real-Time Dashboard**
- Live portfolio values and performance metrics
- Interactive charts powered by Plotly
- Transaction history and detailed logs
- Color-coded events for quick insights

### 🔄 **Flexible Data Sources**
- **Real-time** market data via Polygon API
- **Simulated** prices for offline testing
- **Cached** data to optimize API usage

### 🏗️ **Built for Extension**
- Plug-and-play AI model support (GPT, DeepSeek, Gemini, Grok)
- Modular architecture for easy customization
- MCP protocol for agent-tool communication
- SQLite persistence for reliability

## 🚀 Quick Start

### Prerequisites
- Python 3.8 or higher
- API keys (optional for basic testing)

### Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/vortex-ai.git
cd vortex-ai

# Install dependencies
pip install -r requirements.txt

# Configure environment
cp .env.example .env
# Edit .env with your API keys
```

### Running the Platform

```bash
# Start the monitoring dashboard
python src/app.py

# In a separate terminal, start the trading bots
python src/trading_floor.py
```

Visit `http://localhost:7860` to see your traders in action!

## 🏛️ Architecture

```
┌─────────────────────────────────────────────────┐
│           Gradio Dashboard (app.py)             │
│     Real-time monitoring & visualizations       │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│      Trading Floor (trading_floor.py)           │
│     Orchestrates & schedules trader runs        │
└────────────────┬────────────────────────────────┘
                 │
                 ▼
┌─────────────────────────────────────────────────┐
│         Traders (traders.py)                    │
│  Warren │ George │ Ray │ Cathie                 │
│  Each with unique strategies & AI models        │
└──┬────────────┬────────────┬────────────────┬───┘
   │            │            │                │
   ▼            ▼            ▼                ▼
┌──────┐   ┌─────────┐  ┌────────┐      ┌─────────┐
│Market│   │Accounts │  │Research│      │Database │
│ API  │   │Manager  │  │ Tools  │      │(SQLite) │
└──────┘   └─────────┘  └────────┘      └─────────┘
```

### Core Components

| Component | Purpose | Key File |
|-----------|---------|----------|
| **Dashboard** | Real-time UI for monitoring | `app.py` |
| **Trading Floor** | Scheduler & orchestrator | `trading_floor.py` |
| **Traders** | AI agents with strategies | `traders.py` |
| **Market** | Price fetching & caching | `market.py` |
| **Accounts** | Portfolio management | `accounts.py` |
| **Database** | SQLite persistence | `database.py` |

## 📁 Project Structure

```
Vortex AI/
├── src/
│   ├── app.py                 # Gradio dashboard
│   ├── trading_floor.py       # Agent orchestration
│   ├── traders.py             # Core trading logic
│   ├── market.py              # Market data integration
│   ├── accounts.py            # Account management
│   ├── database.py            # Data persistence
│   ├── templates.py           # AI prompts
│   └── tracers.py             # Logging & tracing
├── data/                      # SQLite database
├── docs/                      # Detailed documentation
├── assets/                    # UI resources
├── .env.example               # Environment template
└── requirements.txt           # Dependencies
```

## 🎮 How It Works

1. **Initialization** - Each trader starts with $10,000 and a unique strategy
2. **Market Analysis** - Traders fetch market data and research insights
3. **Decision Making** - AI models analyze conditions and generate trade decisions
4. **Execution** - Buy/sell orders are placed with realistic spreads (0.2%)
5. **Monitoring** - Dashboard updates in real-time with portfolios and logs
6. **Repeat** - Process runs every 60 minutes (configurable)

## 🛠️ Configuration

Key environment variables in `.env`:

```bash
# Market Data
POLYGON_API_KEY=your_key_here
POLYGON_PLAN=starter  # starter, developer, or premium

# AI Models (optional - use multiple or single)
OPENROUTER_API_KEY=your_key
DEEPSEEK_API_KEY=your_key
GROK_API_KEY=your_key
GEMINI_API_KEY=your_key

# Trading Settings
RUN_EVERY_N_MINUTES=60
INITIAL_BALANCE=10000
USE_MANY_MODELS=true  # Use different models per trader
```

## 📚 Documentation

- **Full Project Guide**: See `docs/Vortex AI.markdown` for in-depth architecture
- **Code Walkthrough**: Check `docs/` for component-specific details
- **Adding Traders**: Customize strategies in `src/reset.py`
- **API Integration**: Extend MCP servers in `mcp_params.py`

## 🔮 Future Enhancements

- 📈 Machine learning for adaptive strategies
- 🌐 WebSocket support for real-time streaming
- 📊 Advanced analytics (Sharpe ratio, volatility metrics)
- 🔐 Authentication & multi-user support
- ⚡ PostgreSQL migration for production scale


## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Inspired by legendary investors: Buffett, Soros, Dalio, and Wood
- Built with [Gradio](https://gradio.app/), [Plotly](https://plotly.com/), and ❤️
- Market data powered by [Polygon.io](https://polygon.io/)

---

<div align="center">

**Made with 🌪️ by [Your Name]**

⭐ Star this repo if you find it useful!

</div>

