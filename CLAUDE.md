# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Language Preference

**重要说明**: 在此项目中，所有回答都应该使用中文。这是一个中文增强版本的项目，专为中文用户设计。Claude Code 在与用户交互时应该：

1. **使用中文回答所有问题** - 包括技术解释、错误分析、建议等
2. **保持代码注释的双语特性** - 代码中的注释可以是中英文结合
3. **文档更新使用中文** - 新增或修改的文档内容应该使用中文
4. **错误信息中文化** - 提供中文的错误解释和解决方案

## Repository Overview

TradingAgents-CN is a Chinese-enhanced fork of TauricResearch/TradingAgents, providing a multi-agent LLM financial trading decision framework with full Chinese localization, Docker containerization, and support for Chinese financial markets.

## Common Development Commands

### Running the Application

```bash
# Web interface (development)
streamlit run web/app.py

# Alternative web startup
python web/run_web.py

# CLI interface
python -m cli.main

# Docker deployment (production)
docker-compose up -d --build

# Stop Docker services
docker-compose down
```

### Testing

```bash
# Run all tests
python -m pytest tests/

# Run specific test file
python -m pytest tests/test_specific.py

# Run with coverage
python -m pytest tests/ --cov=tradingagents

# Run with verbose output
python -m pytest tests/ -v

# Run tests with short traceback
python -m pytest tests/ --tb=short

# Run integration tests
python -m pytest tests/integration/

# Quick validation tests
python tests/quick_test.py
python tests/fast_tdx_test.py

# API tests
python tests/test_all_apis.py
```

### Dependencies Management

```bash
# Install dependencies (using uv - recommended)
uv pip install -r requirements.txt

# Install dependencies (using pip)
pip install -r requirements.txt

# Install database dependencies
pip install -r requirements_db.txt

# Install from pyproject.toml
pip install -e .
```

### Build Commands

```bash
# Build Docker image with PDF support
python scripts/build_docker_with_pdf.py

# Standard Docker build
docker build -t tradingagents-cn:latest .

# Docker Compose build and run
docker-compose up -d --build
```

### Development Tools

```bash
# Check dependencies
python scripts/validation/check_dependencies.py

# Check system status
python scripts/validation/check_system_status.py

# Verify configuration
python scripts/validation/verify_gitignore.py

# Start Docker services (development)
bash scripts/docker/start_docker_services.sh

# Stop Docker services
bash scripts/docker/stop_docker_services.sh
```

## High-Level Architecture

### Multi-Agent System

The core architecture implements a trading firm simulation with specialized agents:

1. **Market Analyst** (`tradingagents/agents/market_analyst.py`): Technical analysis, price trends, volume analysis
2. **Fundamental Analyst** (`tradingagents/agents/fundamental_analyst.py`): Financial data analysis, valuation
3. **News Analyst** (`tradingagents/agents/news_analyst.py`): News sentiment analysis, event impact assessment
4. **Sentiment Analyst** (`tradingagents/agents/sentiment_analyst.py`): Social media sentiment, market fear/greed
5. **Bull/Bear Researchers** (`tradingagents/agents/researcher.py`): Opposing viewpoint analysis
6. **Trader** (`tradingagents/agents/trader.py`): Final trading decisions, position sizing
7. **Risk Manager** (`tradingagents/agents/risk_manager.py`): Risk assessment, portfolio management

Agents coordinate through LangGraph workflows defined in `tradingagents/graph/`.

### Data Flow Architecture

1. **Data Sources** (`tradingagents/dataflows/`):
   - Chinese markets: AkShare, Tushare, BaoStock, PyTDX
   - Global markets: yfinance, FinnHub, EODHD
   - Multi-layer caching: Redis → MongoDB → API → Local

2. **LLM Integration** (`tradingagents/llm_adapters/`):
   - Supports multiple providers: OpenAI, Google AI, Anthropic, DeepSeek, Alibaba DashScope
   - Unified adapter interface for easy provider switching
   - Cost optimization with DeepSeek V3 for Chinese users

3. **Storage & Caching**:
   - MongoDB: Persistent storage for analysis results
   - Redis: High-performance caching layer
   - ChromaDB: Vector database for memory system

### Web Interface

The Streamlit web interface (`web/`) provides:
- Real-time stock analysis with interactive charts
- Multi-agent analysis workflow visualization
- Report generation (PDF/Word/Markdown)
- Configuration management UI
- Token usage monitoring

### Key Configuration Points

1. **Environment Variables** (`.env`):
   - LLM API keys (multiple providers)
   - Database connections (MongoDB, Redis)
   - Chinese data source API keys
   - Feature toggles

2. **Docker Configuration**:
   - Multi-service setup with web, MongoDB, Redis
   - PDF export support with wkhtmltopdf + Xvfb
   - Chinese mirror configuration for faster builds

3. **Chinese Market Support**:
   - A-shares code format: 000000.SZ, 600000.SH
   - Hong Kong stocks: 00700.HK
   - Automatic market detection and routing

## Important Notes for Development

1. **Chinese Localization**: All user-facing text should be in Chinese. Documentation and code comments are bilingual.

2. **Data Source Selection**: The system automatically selects appropriate data sources based on stock market (Chinese vs. international).

3. **Memory System**: Uses ChromaDB with singleton manager pattern to avoid concurrency issues.

4. **Error Handling**: Comprehensive error handling with Chinese error messages for better user experience.

5. **Testing**: When adding new features, ensure compatibility with both Chinese and international markets.

6. **PDF Export**: Requires wkhtmltopdf. In Docker, uses virtual display (Xvfb) for headless operation.

7. **Configuration**: Use `.env` file for API keys and configuration. Copy `.env.example` to `.env` and fill in your API keys.

8. **No Linting Tools**: This project does not use automated linting tools (ruff, black, flake8). Code quality is maintained through manual review and testing.

9. **Environment Variables**: Essential configuration is stored in `.env` file. Key required variables:
   - `DASHSCOPE_API_KEY`: For Alibaba DashScope (Chinese LLM)
   - `FINNHUB_API_KEY`: For US stock data
   - `TUSHARE_TOKEN`: For Chinese stock data (recommended)

10. **Docker Development**: The project is optimized for Docker development. Use `docker-compose up -d --build` for full environment setup including databases.