# Vortex AI

A modular, multi-agent trading simulation platform. Features autonomous trading bots with unique strategies, real or simulated market data, and a Gradio-based dashboard for real-time monitoring. Designed for research, education, and extensibility.

## Features
- Autonomous trading bots (Warren, George, Ray, Cathie)
- Real or simulated market data (Polygon API integration)
- Persistent account and market data (SQLite)
- Gradio web dashboard for live monitoring
- Modular, extensible architecture

## Quick Start
1. Clone the repo
2. Install dependencies: `pip install -r requirements.txt`
3. Copy `.env.example` to `.env` and fill in API keys
4. Run `python src/app.py` for the dashboard
5. Run `python src/trading_floor.py` to start trading bots

## Project Structure
```
Vortex AI/
├── README.md
├── requirements.txt
├── project.toml
├── .env.example
├── src/
├── data/
├── assets/
├── docs/
└── tests/
```

---
See `docs/Vortex AI.markdown` for full details. 