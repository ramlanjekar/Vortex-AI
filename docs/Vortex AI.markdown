# Vortex AI

## 1. Project Overview

The **Vortex AI** is a sophisticated multi-agent trading simulation designed to emulate real-world trading environments. It features autonomous trading bots (agents) that execute buy and sell decisions based on predefined strategies and market data, either simulated or sourced from real-time APIs like Polygon. The platform provides a Gradio-based web interface for monitoring bot performance, portfolios, and transaction logs, making it suitable for research, education, or practical trading simulations. Its modular architecture supports extensibility, allowing integration of various agent models and data sources.

The platform simulates four distinct traders—Warren, George, Ray, and Cathie—each inspired by renowned investors (Warren Buffett, George Soros, Ray Dalio, and Cathie Wood) with unique investment strategies. It leverages modern technologies, including asynchronous Python, SQLite for data persistence, and a Model Context Protocol (MCP) for agent communication, ensuring scalability and robustness.

This report provides a comprehensive overview of the project, including its architecture, functionality, technical details, and potential interview questions, preparing you to confidently present and discuss the project.

---

## 2. Objectives

The primary objectives of the Vortex AI are:
- **Simulate Autonomous Trading**: Enable bots to make independent trading decisions based on their strategies and market conditions.
- **Real-Time Monitoring**: Provide a user-friendly UI to visualize bot portfolios, performance metrics, and logs.
- **Extensibility**: Support multiple agent models (e.g., GPT, DeepSeek, Gemini, Grok) and market data sources.
- **Educational Value**: Facilitate learning about trading strategies, market dynamics, and AI-driven decision-making.
- **Research Integration**: Allow bots to use research tools to inform trading decisions, enhancing realism.
- **Reliability and Scalability**: Ensure robust data persistence, logging, and scheduling for continuous operation.

---

## 3. System Architecture

The platform is built as a modular, distributed system with distinct components for trading logic, market data, account management, UI, and agent communication. Below is an overview of the architecture based on the provided files.

### 3.1 Core Components

1. **Main Application & UI (`app.py`)**:
   - **Purpose**: Serves as the entry point, constructing the Gradio-based web interface.
   - **Functionality**: Displays trader profiles, portfolio values, holdings, transactions, and logs in real-time.
   - **Key Features**:
     - Dynamic UI updates using Gradio timers (every 120 seconds for portfolio, 0.5 seconds for logs).
     - Visualizations (e.g., portfolio value charts using Plotly).
     - Color-coded logs for different event types (e.g., trace, account, agent).

2. **Trading Orchestration (`trading_floor.py`)**:
   - **Purpose**: Manages trader creation and schedules their execution.
   - **Functionality**:
     - Creates trader instances (Warren, George, Ray, Cathie) with distinct models and strategies.
     - Schedules trading runs every `RUN_EVERY_N_MINUTES` (default: 60 minutes) using `asyncio`.
     - Checks market status (`is_market_open`) to skip runs when closed, unless overridden.
     - Supports multiple AI models (e.g., GPT-4o-mini, DeepSeek, Gemini) if `USE_MANY_MODELS` is enabled.

3. **Trading Bot Logic (`traders.py`)**:
   - **Purpose**: Defines the `Trader` class, handling bot logic and interactions with market and research tools.
   - **Functionality**:
     - Initializes traders with names, models, and strategies.
     - Integrates with MCP servers for account operations and research.
     - Alternates between trading (new opportunities) and rebalancing modes.
     - Uses `AsyncExitStack` for managing MCP server connections.
     - Sends push notifications summarizing trades and portfolio health.

4. **Market Data (`market.py`)**:
   - **Purpose**: Fetches and caches stock prices from the Polygon API or simulates prices if unavailable.
   - **Functionality**:
     - Supports real-time (`is_realtime_polygon`), delayed (`is_paid_polygon`), or end-of-day data.
     - Caches market data in SQLite to reduce API calls.
     - Falls back to random prices if Polygon API fails.
     - Handles timezone issues for accurate market data.

5. **Account Management (`accounts.py`, `accounts_client.py`, `accounts_server.py`)**:
   - **Purpose**: Manages trader accounts, including balances, holdings, and transactions.
   - **Functionality**:
     - `accounts.py`: Defines the `Account` class with methods for deposits, withdrawals, buying/selling shares, and portfolio calculations.
     - `accounts_client.py`: Provides async functions to interact with account data via MCP.
     - `accounts_server.py`: Exposes account operations as MCP tools and resources (e.g., `get_balance`, `buy_shares`, `read_account_resource`).
     - Includes a 0.2% spread (`SPREAD`) for buy/sell prices to simulate transaction costs.
     - Persists data in SQLite via `database.py`.

6. **Data Persistence (`database.py`)**:
   - **Purpose**: Stores account data, logs, and market data in an SQLite database (`accounts.db`).
   - **Functionality**:
     - Tables: `accounts` (account details), `logs` (event logs), `market` (cached market data).
     - Supports CRUD operations for persistence and retrieval.
     - Logs include timestamps, types (e.g., account, trace), and messages.

7. **Agent Communication & Prompts (`templates.py`, `mcp_params.py`)**:
   - **Purpose**: Defines instructions for traders and researchers, and configures MCP servers.
   - **Functionality**:
     - `templates.py`: Provides dynamic instructions based on market data availability (real-time, delayed, or end-of-day).
     - `mcp_params.py`: Configures MCP servers for traders (accounts, push notifications, market) and researchers (fetch, Brave search, memory).
     - Supports integration with external APIs (e.g., DeepSeek, Grok, Gemini, OpenRouter).

8. **Logging & Tracing (`tracers.py`)**:
   - **Purpose**: Implements logging and tracing for agent actions and system events.
   - **Functionality**:
     - Uses `LogTracer` to log trace and span events (start/end) to the database.
     - Generates unique trace IDs with `make_trace_id` for tracking.
     - Maps trace IDs to trader names for contextual logging.

9. **Utility (`util.py`)**:
   - **Purpose**: Provides UI enhancements and general utilities.
   - **Functionality**:
     - Defines CSS for styling Gradio UI (e.g., positive/negative PNL colors).
     - Includes JavaScript for forcing dark mode.
     - Defines `Color` enum for consistent log coloring.

### 3.2 System Flow

1. **Initialization**:
   - `app.py` creates the Gradio UI, initializing `TraderView` for each trader.
   - `trading_floor.py` creates `Trader` instances and schedules their runs.

2. **Trading Cycle**:
   - Each trader alternates between trading and rebalancing modes (`traders.py`).
   - Bots fetch account data and strategies via `accounts_client.py`.
   - Research tools (via MCP) provide market insights, and bots execute trades using `accounts_server.py` tools.

3. **Data Flow**:
   - Market data is fetched from Polygon or cached in SQLite (`market.py`, `database.py`).
   - Account updates (e.g., buy/sell) are persisted in SQLite (`accounts.py`, `database.py`).
   - Logs and traces are recorded (`tracers.py`, `database.py`).

4. **UI Updates**:
   - Portfolio values, holdings, and transactions are displayed in real-time via Gradio (`app.py`).
   - Logs are color-coded and updated frequently (`util.py`, `database.py`).

5. **Notifications**:
   - Traders send push notifications via `push_server.py` after trades, summarizing portfolio health.

---

## 4. Key Functionalities

### 4.1 Trading Strategies
Each trader follows a unique strategy defined in `reset.py`:
- **Warren (Buffett)**: Value-oriented, long-term investments in undervalued companies.
- **George (Soros)**: Aggressive, macro-focused trading on market mispricings.
- **Ray (Dalio)**: Systematic, diversified approach using risk parity.
- **Cathie (Wood)**: Focuses on disruptive innovation, particularly crypto ETFs.

### 4.2 Account Management
- Initial balance: $10,000 (`INITIAL_BALANCE`).
- Supports deposits, withdrawals, and buy/sell transactions with a 0.2% spread.
- Tracks holdings, transactions, and portfolio value time series.

### 4.3 Market Data Integration
- Uses Polygon API for real-time or end-of-day data.
- Caches data to optimize API usage.
- Falls back to random prices for robustness.

### 4.4 UI Dashboard
- Displays trader profiles, portfolio values, holdings, transactions, and logs.
- Includes interactive Plotly charts for portfolio value trends.
- Updates dynamically with Gradio timers.

### 4.5 Research Integration
- Traders use a `Researcher` agent (via MCP) to fetch news and opportunities.
- Supports web searches and knowledge graph storage for persistent learning.

### 4.6 Scheduling
- Runs every 60 minutes (configurable via `RUN_EVERY_N_MINUTES`).
- Skips runs when the market is closed unless overridden.

---

## 5. Technical Details

### 5.1 Technologies Used
- **Python Libraries**:
  - `gradio`: Web UI framework.
  - `plotly`: Interactive charting.
  - `pydantic`: Data validation for `Account` and `Transaction` models.
  - `sqlite3`: Database for persistence.
  - `polygon`: Market data API client.
  - `requests`: HTTP requests for push notifications.
  - `asyncio`: Asynchronous programming for trader execution and MCP communication.
- **External APIs**:
  - Polygon (market data).
  - DeepSeek, Grok, Gemini, OpenRouter (AI models).
  - Pushover (notifications).
  - Brave (search for research).
- **MCP**: Custom protocol for agent-tool communication.
- **Environment Management**: `dotenv` for configuration.

### 5.2 Key Design Choices
- **Asynchronous Programming**: Uses `asyncio` for non-blocking trader execution and MCP interactions.
- **Modular Architecture**: Separates concerns (UI, trading, accounts, market, persistence) for maintainability.
- **Data Persistence**: SQLite ensures lightweight, file-based storage.
- **Error Handling**: Robust fallbacks (e.g., random prices if Polygon fails).
- **Extensibility**: Supports multiple AI models and MCP servers for flexibility.

### 5.3 Challenges and Solutions
- **API Rate Limits**: Caches market data in SQLite to reduce Polygon API calls.
- **Real-Time Updates**: Gradio timers ensure frequent UI refreshes without overloading.
- **Agent Coordination**: MCP and `AsyncExitStack` manage complex server interactions.
- **Strategy Diversity**: Predefined strategies in `reset.py` allow varied trading behaviors.

---

## 6. Project Setup and Execution

### 6.1 Prerequisites
- Python 3.8+.
- Required libraries: `gradio`, `plotly`, `pydantic`, `sqlite3`, `polygon`, `requests`, `python-dotenv`.
- API keys: Polygon, DeepSeek, Grok, Gemini, OpenRouter, Pushover, Brave.
- Environment variables set in `.env` file.

### 6.2 Setup Instructions
1. Clone the repository containing the `6_mcp` folder.
2. Install dependencies: `pip install -r requirements.txt`.
3. Configure `.env` with API keys and settings (e.g., `RUN_EVERY_N_MINUTES`, `POLYGON_PLAN`).
4. Initialize the database: Run `database.py` to create `accounts.db`.
5. Reset trader strategies: Run `reset.py`.
6. Start the UI: Run `app.py` to launch the Gradio interface.
7. Run the trading scheduler: Execute `trading_floor.py` for scheduled trading.

### 6.3 Running the Application
- **UI**: `python app.py` launches the Gradio dashboard.
- **Scheduler**: `python trading_floor.py` runs traders every 60 minutes.
- **Servers**: `accounts_server.py`, `market_server.py`, `push_server.py` must be running for MCP communication.

---

## 7. Potential Interview Questions and Answers

### 7.1 General Questions
**Q: What is the purpose of the Vortex AI?**
- **A**: It’s a multi-agent trading simulation platform that allows autonomous bots to trade based on predefined strategies, using real or simulated market data. It provides a Gradio UI to monitor performance and is designed for educational and research purposes, with extensibility for different AI models and data sources.

**Q: How does the platform ensure scalability?**
- **A**: The modular architecture separates concerns (UI, trading, accounts, market), and `asyncio` enables non-blocking execution. SQLite provides lightweight persistence, and MCP servers allow distributed agent communication, making it easy to add new traders or tools.

**Q: What challenges did you face while developing the platform?**
- **A**: Handling API rate limits was a challenge, addressed by caching market data in SQLite. Coordinating multiple async MCP servers was complex, solved using `AsyncExitStack`. Ensuring UI responsiveness required fine-tuning Gradio timers.

### 7.2 Technical Questions
**Q: How does the platform handle market data?**
- **A**: The `market.py` module fetches data from the Polygon API, supporting real-time, delayed, or end-of-day prices based on the `POLYGON_PLAN`. Data is cached in SQLite to reduce API calls, and a random price fallback ensures robustness if the API fails.

**Q: Explain the role of MCP in the platform.**
- **A**: MCP (Model Context Protocol) facilitates communication between traders and tools (e.g., account operations, market data, research). `mcp_params.py` configures servers, and `accounts_server.py`, `market_server.py`, and `push_server.py` expose tools via MCP, enabling asynchronous, distributed interactions.

**Q: How are trader strategies implemented?**
- **A**: Strategies are defined in `reset.py` for each trader (e.g., Warren’s value investing, Cathie’s crypto focus). The `Trader` class in `traders.py` uses these strategies, fetched via `read_strategy_resource`, to guide trading decisions, with research tools providing additional insights.

**Q: How does the UI update in real-time?**
- **A**: The Gradio UI in `app.py` uses timers to refresh portfolio data (every 120 seconds) and logs (every 0.5 seconds). The `TraderView` class fetches updated data from `accounts.py` and `database.py`, rendering it with Plotly charts and color-coded logs.

### 7.3 Design and Architecture Questions
**Q: Why use SQLite instead of a more robust database like PostgreSQL?**
- **A**: SQLite was chosen for its simplicity and lightweight nature, suitable for a simulation environment with moderate data volumes. It requires no external server setup, making it ideal for educational or prototype use. For production, a more robust database could be integrated.

**Q: How does the platform handle errors during trading?**
- **A**: Errors are caught in `traders.py` during `run_with_trace`, logged via `tracers.py`, and printed for debugging. The `market.py` module includes fallbacks (e.g., random prices), and account operations validate funds and holdings to prevent invalid trades.

**Q: How is the platform extensible for new traders or models?**
- **A**: New traders can be added by updating `names`, `lastnames`, and `model_names` in `trading_floor.py`. New models are supported via `get_model` in `traders.py`, which integrates with various AI APIs. MCP servers can be extended in `mcp_params.py` for additional tools.

### 7.4 Scenario-Based Questions
**Q: If the Polygon API fails, how does the platform respond?**
- **A**: The `market.py` module falls back to random prices between 1 and 100, ensuring trading can continue. This is logged, and cached data in SQLite is used when available to minimize disruptions.

**Q: How would you add a new trading strategy?**
- **A**: Define the strategy in `reset.py` with a new trader name and description. Update `trading_floor.py` to include the trader in `names` and `lastnames`. The `Trader` class will automatically use the new strategy via `read_strategy_resource`.

**Q: How would you improve the platform’s performance?**
- **A**: Optimize SQLite queries with indexes, reduce API calls with longer caching, and parallelize trader runs further with `asyncio.gather`. For UI, implement lazy loading for large datasets and optimize Plotly chart rendering.

---

## 8. Future Enhancements

- **Advanced Strategies**: Incorporate machine learning for dynamic strategy adaptation.
- **Real-Time Trading**: Enhance real-time data integration for production use.
- **Expanded UI Features**: Add interactive controls for manual trading or strategy overrides.
- **Security**: Implement authentication for API keys and sensitive operations.
- **Scalability**: Migrate to a distributed database (e.g., PostgreSQL) for larger-scale simulations.
- **Analytics**: Add advanced metrics (e.g., Sharpe ratio, volatility) to the UI.

---

## 9. Conclusion

The Vortex AI is a robust, extensible system that effectively simulates autonomous trading with a user-friendly interface. Its modular design, asynchronous architecture, and integration with real-world APIs make it a valuable tool for learning and experimentation. By understanding the roles of each component (`app.py`, `traders.py`, `market.py`, etc.), you can confidently explain the project’s functionality, design choices, and potential improvements in an interview setting.