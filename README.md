# 亚马逊商品优化助手 (AMZ Product Optimizer)

> 一站式亚马逊商品优化解决方案，帮助卖家自动完成从关键词分析到商品详情图生成的全流程优化。

**版本**: v2.0.0  
**作者**: 北野川  
**组织**: bug 砖家  
**语言**: 中文

## ✨ 核心特性

- 🔍 **热搜词智能分析** - 实时抓取 AMZ123 美国站 TOP25W 搜索词数据
- 📝 **商品名自动优化** - 遵循 [品牌名]+[核心关键词]+[产品特性]+[规格] 标准结构
- 🎨 **AI 详情图生成** - 自动生成 5 张场景化商品详情图，提升转化率
- 📊 **点击率监控** - 定时监控主图表现，<5% 自动标记优化
- 📋 **表格自动回写** - 优化结果实时同步到钉钉 AI 表格

## 🚀 快速开始

### 1. 安装依赖

```bash
pip install requests>=2.28.0
pip install beautifulsoup4>=4.11.0
```

### 2. 配置 MCP 服务

确保已配置以下钉钉 MCP 服务：

- **dingtalk** (ID: `19ce1741e7b`) - 钉钉 AI 表格操作
- **淘宝 opc 服务** (ID: `19cf03a191f`) - 淘宝图片生成

### 3. 使用示例

#### 示例 1: 完整优化流程

```python
from scripts.product_optimizer import AMZProductOptimizer

config = {
    'keyword': 'cat food',
    'baseId': '你的表格 BaseID',
    'tableId': 'QlP62Ie',
    'mode': 'full'
}

optimizer = AMZProductOptimizer(config)
result = optimizer.run()
```

#### 示例 2: 仅获取热搜词

```python
config = {
    'keyword': 'dog bed',
    'mode': 'keywords_only'
}

optimizer = AMZProductOptimizer(config)
keywords = optimizer.fetch_hot_keywords('dog bed')
```

#### 示例 3: 监控点击率

```python
config = {
    'baseId': '你的表格 BaseID',
    'mode': 'monitor'
}

optimizer = AMZProductOptimizer(config)
report = optimizer.monitor_click_rate()
print(f"需要优化的商品数：{report['need_optimization']}")
```

## 📖 详细文档

请查看 [SKILL.md](./SKILL.md) 获取完整的使用说明、API 参考和最佳实践。

## 🎯 适用场景

- ✅ 亚马逊跨境电商卖家选品上架
- ✅ 1688 供应商为亚马逊卖家供货
- ✅ 电商产品经理优化商品列表
- ✅ 跨境电商运营人员批量优化商品信息

## ⚠️ 重要注意事项

### 商品名优化规范

❌ **错误示范**:
```
Blue Buffalo Cat Food Hot Search: wet cat food, dry cat food
```

✅ **正确示范**:
```
Blue Buffalo Tastefuls Flaked Wet Cat Food, High Protein Natural Ingredients with Real Fish, 3 oz Cans (Pack of 24)
```

**严禁**:
- 出现"Hot Search"等标记词（会被平台判定违规）
- 关键词堆砌（标题必须是通顺的完整描述）
- 不通顺的语句

### 详情图生成策略

| 图片位置 | 场景 | 选择理由 |
|---------|------|---------|
| 主图 | 温馨客厅 | 展示整体效果和使用场景，吸引点击 |
| 详情图 1 | 卧室床边 | 生活化场景，增强代入感 |
| 详情图 2 | 产品细节 | 展示工艺和材质细节 |
| 详情图 3 | 宠物使用 | 营销转化场景，激发购买欲 |
| 详情图 4 | 阳光阅读角 | 生活方式展示 |

## 📊 执行模式

| 模式 | 说明 | 适用场景 |
|------|------|----------|
| `full` | 完整流程：热搜词 + 优化 + 绘图 | 首次全面优化 |
| `keywords_only` | 仅获取热搜词 | 关键词分析 |
| `optimize_names` | 仅优化商品名 | 已有热搜词，只需扩写 |
| `generate_images` | 仅生成详情图 | 商品名已优化，需配图 |
| `monitor` | 仅监控点击率 | 日常数据监控 |

## 🔧 故障排查

### 常见问题

**Q: 获取热搜词失败**  
A: 检查 AMZ123 网站是否可访问，可能需要浏览器自动化支持。

**Q: 图片生成超时**  
A: 淘宝 MCP 服务并发限制，建议降低批量大小或增加延时。

**Q: 写入表格失败**  
A: 确认字段类型匹配（url vs attachment），检查权限配置。

**Q: 商品名优化不符合预期**  
A: 检查是否遵循了标准结构，确保没有出现"Hot Search"等标记词。

更多问题请查看 [SKILL.md](./SKILL.md) 的故障排查章节。

## 📈 版本历史

### v2.0.0 (2026-03-16)
- ✅ 修复商品名优化策略，移除"Hot Search"标签
- ✅ 遵循电商标题标准结构
- ✅ 避免关键词堆砌，保持语句通顺
- ✅ 优化详情图生成 prompt，增强场景差异
- ✅ 完善主图点击率监控机制

### v1.0.0 (2026-03-15)
- ✅ AMZ123 热搜词抓取
- ✅ 智能商品名扩写
- ✅ 淘宝 MCP 详情图生成
- ✅ 主图点击率监控
- ✅ 钉钉表格自动回写

## 📄 许可证

本技能包归创作者所有，未经许可不得用于商业用途。

## 🤝 技术支持

- **作者**: 北野川
- **组织**: bug 砖家
- **联系方式**: 钉钉内部联系

---

**最后更新**: 2026-03-16
