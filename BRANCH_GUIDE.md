# 🌿 分支管理快速指南

## 🚀 快速开始

### 常用命令

```bash
# 查看所有分支
python scripts/branch_manager.py list

# 创建功能分支
python scripts/branch_manager.py create feature 分支名称 -d "功能描述"

# 创建中文增强分支
python scripts/branch_manager.py create enhancement 分支名称 -d "增强描述"

# 切换分支
python scripts/branch_manager.py switch 分支名称

# 删除分支
python scripts/branch_manager.py delete 分支名称

# 清理已合并分支
python scripts/branch_manager.py cleanup
```

## 🏗️ 分支架构

```
main (生产分支) ← 稳定版本，受保护
├── develop (开发主分支) ← 集成所有开发
├── feature/* (功能开发) ← 新功能开发
├── enhancement/* (中文增强) ← 本地化功能
├── hotfix/* (紧急修复) ← Bug修复
└── release/* (发布准备) ← 版本发布
```

## 📋 开发工作流

### 1. 功能开发流程

```bash
# 1. 创建功能分支
python scripts/branch_manager.py create feature portfolio-optimization -d "投资组合优化功能"

# 2. 开发功能
# 编写代码...
git add .
git commit -m "feat: 添加投资组合优化算法"

# 3. 推送到远程
git push origin feature/portfolio-optimization

# 4. 创建PR到develop分支
# 在GitHub上创建Pull Request

# 5. 代码审查和合并
# 审查通过后合并到develop

# 6. 清理分支
python scripts/branch_manager.py delete feature/portfolio-optimization
```

### 2. 中文增强流程

```bash
# 1. 创建增强分支
python scripts/branch_manager.py create enhancement tushare-integration -d "集成Tushare数据源"

# 2. 开发中文功能
# 编写代码...
git add .
git commit -m "enhance: 集成Tushare A股数据"

# 3. 更新中文文档
# 更新docs/目录下的相关文档
git add docs/
git commit -m "docs: 更新Tushare集成文档"

# 4. 推送和合并
git push origin enhancement/tushare-integration
# 创建PR到develop
```

### 3. 紧急修复流程

```bash
# 1. 从main创建修复分支
python scripts/branch_manager.py create hotfix api-timeout-fix -d "修复API超时问题"

# 2. 快速修复
# 修复代码...
git add .
git commit -m "fix: 修复API请求超时问题"

# 3. 推送到main
git push origin hotfix/api-timeout-fix
# 创建PR到main，立即合并

# 4. 同步到develop
git checkout develop
git merge main
git push origin develop
```

## 🎯 分支命名规范

### 功能分支 (feature/)
```bash
feature/portfolio-analysis      # 投资组合分析
feature/risk-management        # 风险管理
feature/backtesting-engine     # 回测引擎
feature/real-time-data         # 实时数据
```

### 中文增强分支 (enhancement/)
```bash
enhancement/baidu-llm          # 百度LLM集成
enhancement/tushare-data       # Tushare数据源
enhancement/chinese-terms      # 中文金融术语
enhancement/akshare-api        # AkShare API集成
```

### 修复分支 (hotfix/)
```bash
hotfix/memory-leak             # 内存泄漏修复
hotfix/config-error            # 配置错误修复
hotfix/api-rate-limit          # API限流修复
```

### 发布分支 (release/)
```bash
release/v1.1.0-cn             # 版本发布准备
release/v1.2.0-cn-beta        # Beta版本
```

## 🔧 实用技巧

### 查看分支状态
```bash
# 查看当前分支
git branch --show-current

# 查看所有分支
git branch -a

# 查看分支关系
git log --graph --oneline --all

# 查看未合并分支
git branch --no-merged develop
```

### 分支同步
```bash
# 同步develop分支
git checkout develop
git pull origin develop

# 将develop合并到功能分支
git checkout feature/your-feature
git merge develop

# 或者使用rebase
git rebase develop
```

### 分支清理
```bash
# 删除本地已合并分支
git branch --merged develop | grep -v "develop\|main" | xargs -n 1 git branch -d

# 删除远程跟踪分支
git remote prune origin

# 使用我们的工具清理
python scripts/branch_manager.py cleanup
```

## 📊 分支保护规则

### main分支
- ✅ 要求PR审查
- ✅ 要求CI通过
- ✅ 禁止直接推送
- ✅ 禁止强制推送

### develop分支
- ✅ 要求PR审查
- ✅ 要求CI通过
- ✅ 允许管理员绕过

### 功能分支
- ❌ 无特殊限制
- ✅ 自动删除已合并分支

## 🚨 注意事项

### 开发建议
1. **小而频繁的提交** - 每个提交解决一个问题
2. **描述性分支名** - 清楚表达分支用途
3. **及时同步** - 定期从develop拉取更新
4. **完整测试** - 合并前确保测试通过
5. **文档同步** - 功能开发同时更新文档

### 避免的操作
1. **直接推送到main** - 始终通过PR
2. **长期分支** - 功能分支应该短期完成
3. **大型合并** - 避免一次性合并大量更改
4. **跳过测试** - 合并前必须通过所有测试
5. **忽略冲突** - 仔细解决每个合并冲突

## 🔗 相关资源

- **详细策略**: [docs/development/branch-strategy.md](docs/development/branch-strategy.md)
- **分支管理工具**: [scripts/branch_manager.py](scripts/branch_manager.py)
- **GitHub工作流**: [.github/workflows/](/.github/workflows/)

## 📞 获取帮助

```bash
# 查看工具帮助
python scripts/branch_manager.py --help

# 查看特定命令帮助
python scripts/branch_manager.py create --help
```

### 联系方式
- **GitHub Issues**: [提交问题](https://github.com/hsliuping/TradingAgents-CN/issues)
- **邮箱**: hsliup@163.com

---

通过这套分支管理体系，您可以高效地进行功能开发和项目维护！🚀
