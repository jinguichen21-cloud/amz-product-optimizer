# amz-product-optimizer 技能安装指南

## 📋 前置要求

### 1. Python 环境
- Python >= 3.9
- pip >= 21.0

### 2. MCP 服务配置

必须已配置以下钉钉 MCP 服务：

| 服务名称 | Server ID | 必需工具 |
|---------|----------|---------|
| **dingtalk** | `19ce1741e7b` | query_records, update_records, get_tables |
| **淘宝 opc 服务** | `19cf03a191f` | create_picture_from_tb, query_picture_from_tb |

### 3. 钉钉 AI 表格

- 已创建商品数据表
- 包含必需字段 (见 [table_schema.md](./references/table_schema.md))
- 具有读写权限

---

## 🚀 安装步骤

### 方法 1: 通过 Real CLI 安装 (推荐)

```bash
# 进入技能目录
cd /Users/kitano/.real/workspace/amz-product-optimizer-skill

# 使用 real_cli 安装技能
real_cli skills install local --json '{"path": "/Users/kitano/.real/workspace/amz-product-optimizer-skill"}'
```

### 方法 2: 手动安装

#### 步骤 1: 复制技能文件

```bash
# 将技能复制到.skills 目录
cp -r /Users/kitano/.real/workspace/amz-product-optimizer-skill \
      /Users/kitano/.real/.skills/amz-product-optimizer
```

#### 步骤 2: 安装 Python 依赖

```bash
cd /Users/kitano/.real/.skills/amz-product-optimizer
pip3 install requests>=2.28.0 beautifulsoup4>=4.11.0
```

#### 步骤 3: 验证安装

```bash
python3 scripts/product_optimizer.py --help
```

---

## ⚙️ 配置说明

### 1. 修改 skill.json

编辑 `/Users/kitano/.real/.skills/amz-product-optimizer/skill.json`:

```json
{
  "input_schema": {
    "properties": {
      "baseId": {
        "default": "你的表格 BaseID"
      },
      "tableId": {
        "default": "你的表格 ID"
      }
    }
  }
}
```

### 2. 配置字段映射

编辑 `scripts/product_optimizer.py`:

```python
self.field_mapping = {
    'original_name': '你的原始商品名字段 ID',
    'optimized_name': '你的优化商品名字段 ID',
    'main_image': '你的主图字段 ID',
    'detail_images': ['你的详情图 1 字段 ID', ...],
    'click_rate': '你的点击率字段 ID'
}
```

### 3. 设置定时任务 (可选)

编辑 `hooks/hooks.json`:

```json
{
  "triggers": {
    "scheduled": {
      "cron": "0 10 * * *",
      "timezone": "Asia/Shanghai"
    }
  }
}
```

---

## ✅ 验证安装

### 运行测试用例

```bash
cd /Users/kitano/.real/.skills/amz-product-optimizer

# 运行自动化测试
python3 -m pytest test_cases/ -v
```

### 执行示例任务

```python
from scripts.product_optimizer import AMZProductOptimizer

config = {
    'keyword': 'cat food',
    'baseId': '你的表格 BaseID',
    'tableId': '你的表格 ID',
    'mode': 'keywords_only'  # 仅获取热搜词，不写入表格
}

optimizer = AMZProductOptimizer(config)
result = optimizer.run()
print(f"获取热搜词：{result['keywordsCount']}个")
```

---

## 🔧 故障排查

### 问题 1: 依赖导入失败

**错误**: `ModuleNotFoundError: No module named 'requests'`

**解决**:
```bash
pip3 install requests beautifulsoup4
```

### 问题 2: MCP 服务未找到

**错误**: `Server not found: 19ce1741e7b`

**解决**: 
1. 检查 MCP 服务是否已启用
2. 确认 Server ID 正确
3. 联系管理员配置权限

### 问题 3: 表格写入失败

**错误**: `Table write error: Field not found`

**解决**:
1. 检查字段 ID 是否正确
2. 确认表格存在且可访问
3. 验证字段类型匹配

### 问题 4: 图片生成超时

**错误**: `TimeoutError: Image generation timeout`

**解决**:
1. 检查原图链接是否可访问
2. 降低并发数量
3. 增加超时时间

---

## 📊 性能基准

在标准环境下 (Python 3.9, 网络正常):

| 操作 | 耗时 | 备注 |
|------|------|------|
| 获取 50 个热搜词 | ~5 秒 | 取决于 AMZ123 响应速度 |
| 优化单个商品名 |<0.1 秒 | 纯本地计算 |
| 生成单张图片 | 15-30 秒 | 淘宝 MCP 服务处理 |
| 写入单条记录 | ~1 秒 | 包含 6 个字段 |
| **完整流程 (2 个商品)** | ~45 秒 | 包含 10 张图片生成 |

---

## 🎯 最佳实践

### 1. 批量处理

建议每批处理 2-5 个商品，避免单次请求过多。

```python
# 分批处理
products = get_products_to_optimize()
for batch in chunks(products, 5):
    for product in batch:
        optimize_product(product)
    time.sleep(2)  # 避免频率限制
```

### 2. 错误重试

对于网络相关操作，实现重试机制。

```python
from tenacity import retry, stop_after_attempt, wait_exponential

@retry(stop=stop_after_attempt(3), wait=wait_exponential())
def generate_image(prompt):
    return call_mcp_tool('create_picture_from_tb', {...})
```

### 3. 日志记录

记录每次执行的详细信息，便于排查问题。

```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)
```

---

## 📞 获取帮助

如遇到安装问题，请查看：

1. [README.md](./README.md) - 快速开始指南
2. [SKILL.md](./SKILL.md) - 完整技能文档
3. [DELIVERY_SUMMARY.md](./DELIVERY_SUMMARY.md) - 交付总结
4. [references/table_schema.md](./references/table_schema.md) - 表格字段规范

**技术支持**: 北野川 (钉钉内部联系)

---

## 📄 许可证

本技能包归创作者所有，未经许可不得用于商业用途。

---

**最后更新**: 2026-03-16  
**版本**: v2.0.0
