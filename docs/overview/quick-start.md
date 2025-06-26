# 快速开始指南

## 概述

本指南将帮助您快速上手 TradingAgents 框架，从安装到运行第一个交易分析，只需几分钟时间。

## 前置要求

### 系统要求
- **操作系统**: Windows 10+, macOS 10.15+, 或 Linux
- **Python**: 3.10 或更高版本
- **内存**: 至少 4GB RAM (推荐 8GB+)
- **存储**: 至少 2GB 可用空间

### API 密钥
在开始之前，您需要获取以下API密钥：

1. **OpenAI API Key** (必需)
   - 访问 [OpenAI Platform](https://platform.openai.com/)
   - 创建账户并获取API密钥

2. **FinnHub API Key** (必需)
   - 访问 [FinnHub](https://finnhub.io/)
   - 注册免费账户并获取API密钥

3. **其他API密钥** (可选)
   - Google AI API (如需使用Gemini模型)
   - Anthropic API (如需使用Claude模型)

## 快速安装

### 1. 克隆项目
```bash
git clone https://github.com/TauricResearch/TradingAgents.git
cd TradingAgents
```

### 2. 创建虚拟环境
```bash
# 使用 conda
conda create -n tradingagents python=3.13
conda activate tradingagents

# 或使用 venv
python -m venv tradingagents
source tradingagents/bin/activate  # Linux/macOS
# tradingagents\Scripts\activate  # Windows
```

### 3. 安装依赖
```bash
pip install -r requirements.txt
```

### 4. 配置环境变量
```bash
# Linux/macOS
export OPENAI_API_KEY="your_openai_api_key"
export FINNHUB_API_KEY="your_finnhub_api_key"

# Windows
set OPENAI_API_KEY=your_openai_api_key
set FINNHUB_API_KEY=your_finnhub_api_key
```

或者创建 `.env` 文件：
```bash
# .env 文件
OPENAI_API_KEY=your_openai_api_key
FINNHUB_API_KEY=your_finnhub_api_key
```

## 第一次运行

### 使用命令行界面 (CLI)

最简单的开始方式是使用交互式CLI：

```bash
python -m cli.main
```

这将启动一个交互式界面，您可以：
1. 选择要分析的股票代码
2. 设置分析日期
3. 选择LLM模型
4. 配置分析深度
5. 开始分析

### 使用 Python API

创建一个简单的Python脚本：

```python
# quick_start.py
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG

# 创建配置
config = DEFAULT_CONFIG.copy()
config["deep_think_llm"] = "gpt-4o-mini"  # 使用较便宜的模型进行测试
config["quick_think_llm"] = "gpt-4o-mini"
config["max_debate_rounds"] = 1  # 减少辩论轮次以节省成本
config["online_tools"] = True  # 使用在线数据

# 初始化交易智能体图
ta = TradingAgentsGraph(debug=True, config=config)

# 执行分析
print("开始分析 AAPL...")
state, decision = ta.propagate("AAPL", "2024-01-15")

# 输出结果
print("\n=== 分析结果 ===")
print(f"推荐动作: {decision.get('action', 'hold')}")
print(f"置信度: {decision.get('confidence', 0.5):.2f}")
print(f"风险评分: {decision.get('risk_score', 0.5):.2f}")
print(f"推理过程: {decision.get('reasoning', 'N/A')}")
```

运行脚本：
```bash
python quick_start.py
```

## 配置选项

### 基本配置
```python
config = {
    # LLM 设置
    "llm_provider": "openai",           # 或 "anthropic", "google"
    "deep_think_llm": "gpt-4o-mini",    # 深度思考模型
    "quick_think_llm": "gpt-4o-mini",   # 快速思考模型
    
    # 辩论设置
    "max_debate_rounds": 1,             # 辩论轮次 (1-5)
    "max_risk_discuss_rounds": 1,       # 风险讨论轮次
    
    # 数据设置
    "online_tools": True,               # 使用在线数据
}
```

### 智能体选择
```python
# 选择要使用的分析师
selected_analysts = [
    "market",        # 技术分析师
    "fundamentals",  # 基本面分析师
    "news",         # 新闻分析师
    "social"        # 社交媒体分析师
]

ta = TradingAgentsGraph(
    selected_analysts=selected_analysts,
    debug=True,
    config=config
)
```

## 示例分析流程

### 完整的分析示例
```python
from tradingagents.graph.trading_graph import TradingAgentsGraph
from tradingagents.default_config import DEFAULT_CONFIG
import json

def analyze_stock(symbol, date):
    """分析指定股票"""
    
    # 配置
    config = DEFAULT_CONFIG.copy()
    config["deep_think_llm"] = "gpt-4o-mini"
    config["quick_think_llm"] = "gpt-4o-mini"
    config["max_debate_rounds"] = 2
    config["online_tools"] = True
    
    # 创建分析器
    ta = TradingAgentsGraph(
        selected_analysts=["market", "fundamentals", "news", "social"],
        debug=True,
        config=config
    )
    
    print(f"正在分析 {symbol} ({date})...")
    
    try:
        # 执行分析
        state, decision = ta.propagate(symbol, date)
        
        # 输出详细结果
        print("\n" + "="*50)
        print(f"股票: {symbol}")
        print(f"日期: {date}")
        print("="*50)
        
        print(f"\n📊 最终决策:")
        print(f"  动作: {decision.get('action', 'hold').upper()}")
        print(f"  数量: {decision.get('quantity', 0)}")
        print(f"  置信度: {decision.get('confidence', 0.5):.1%}")
        print(f"  风险评分: {decision.get('risk_score', 0.5):.1%}")
        
        print(f"\n💭 推理过程:")
        print(f"  {decision.get('reasoning', 'N/A')}")
        
        # 分析师报告摘要
        if hasattr(state, 'analyst_reports'):
            print(f"\n📈 分析师报告摘要:")
            for analyst, report in state.analyst_reports.items():
                score = report.get('overall_score', report.get('score', 0.5))
                print(f"  {analyst}: {score:.1%}")
        
        return decision
        
    except Exception as e:
        print(f"❌ 分析失败: {e}")
        return None

# 运行示例
if __name__ == "__main__":
    # 分析苹果公司股票
    result = analyze_stock("AAPL", "2024-01-15")
    
    if result:
        print("\n✅ 分析完成!")
    else:
        print("\n❌ 分析失败!")
```

## 常见问题解决

### 1. API 密钥错误
```
错误: OpenAI API key not found
解决: 确保正确设置了 OPENAI_API_KEY 环境变量
```

### 2. 网络连接问题
```
错误: Connection timeout
解决: 检查网络连接，或使用代理设置
```

### 3. 内存不足
```
错误: Out of memory
解决: 减少 max_debate_rounds 或使用更小的模型
```

### 4. 数据获取失败
```
错误: Failed to fetch data
解决: 检查 FINNHUB_API_KEY 是否正确，或稍后重试
```

## 成本控制建议

### 1. 使用较小的模型
```python
config["deep_think_llm"] = "gpt-4o-mini"    # 而不是 "gpt-4o"
config["quick_think_llm"] = "gpt-4o-mini"   # 而不是 "gpt-4o"
```

### 2. 减少辩论轮次
```python
config["max_debate_rounds"] = 1              # 而不是 3-5
config["max_risk_discuss_rounds"] = 1        # 而不是 2-3
```

### 3. 选择性使用分析师
```python
# 只使用核心分析师
selected_analysts = ["market", "fundamentals"]  # 而不是全部四个
```

### 4. 使用缓存数据
```python
config["online_tools"] = False  # 使用缓存数据而不是实时数据
```

## 下一步

现在您已经成功运行了第一个分析，可以：

1. **探索更多功能**: 查看 [API参考文档](../api/core-api.md)
2. **自定义配置**: 阅读 [配置指南](../configuration/config-guide.md)
3. **开发自定义智能体**: 参考 [扩展开发指南](../development/extending.md)
4. **查看更多示例**: 浏览 [示例和教程](../examples/basic-examples.md)

## 获取帮助

如果遇到问题，可以：
- 查看 [常见问题](../faq/faq.md)
- 访问 [GitHub Issues](https://github.com/TauricResearch/TradingAgents/issues)
- 加入 [Discord 社区](https://discord.com/invite/hk9PGKShPK)
- 查看 [故障排除指南](../faq/troubleshooting.md)

祝您使用愉快！🚀
