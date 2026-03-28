# 钉钉 AI 表格字段规范

## 确认商家商品表 (QlP62Ie)

### 字段映射

| 字段名称 | 字段 ID | 类型 | 说明 | 示例 |
|---------|--------|------|------|------|
| 原始商品名 | `yuhi6x4` | 文本 | 亚马逊原始商品标题 | `Blue Buffalo Tastefuls Flaked Wet Cat Food` |
| 优化后的商品名 | `0z3U03f` | 文本 | 优化后的电商标题 | `Blue Buffalo Tastefuls Flaked Wet Cat Food, High Protein...` |
| 原始图片链接 | `rngdd7i` | 文本 | 亚马逊原图 URL | `https://m.media-amazon.com/images/I/91Lxks7Ex6L._AC_SX679_.jpg` |
| 主图 | `fXqD3tg` | 附件 | AI 生成的主图 | `[{"url": "https://..."}]` |
| 详情图 1 | `fm413aQ` | 附件 | 场景图 1（卧室） | `[{"url": "https://..."}]` |
| 详情图 2 | `L46geMD` | 附件 | 场景图 2（细节） | `[{"url": "https://..."}]` |
| 详情图 3 | `yjglrX6` | 附件 | 场景图 3（使用） | `[{"url": "https://..."}]` |
| 详情图 4 | `sNF7cKY` | 附件 | 场景图 4（阅读角） | `[{"url": "https://..."}]` |
| 详情图 5 | `2OxgAvb` | 附件 | 场景图 5（复用主图） | `[{"url": "https://..."}]` |
| 主图点击率 | `NZPaPX6` | 文本 | 主图点击率数据 | `3.2%` |

### 写入格式

#### 文本字段
```json
{
  "0z3U03f": "Blue Buffalo Tastefuls Flaked Wet Cat Food, High Protein Natural Ingredients with Real Fish, 3 oz Cans (Pack of 24)"
}
```

#### 附件字段
```json
{
  "fXqD3tg": [{"url": "https://is-content-gen.oss-cn-zhangjiakou.aliyuncs.com/out_painting/out_painting_final_result_dir/2026-03-15/5c4d971a5d9f905ff17a4b5e93835777.png"}]
}
```

**注意**: 
- 附件字段必须传入数组格式 `[{"url": "..."}]`
- 服务端会自动代拉外链并转存
- URL 必须是公开可访问的 HTTP/HTTPS 链接

### 查询示例

```python
# 查询所有 cat food 相关商品
records = call_mcp_tool('query_records', {
    'baseId': 'lyQod3RxJKlwxrz9SOpoelQM8kb4Mw9r',
    'tableId': 'QlP62Ie',
    'keyword': 'cat food',
    'fieldIds': ['yuhi6x4', 'rngdd7i', '0z3U03f', 'fXqD3tg']
})
```

### 更新示例

```python
# 更新单个记录
call_mcp_tool('update_records', {
    'baseId': 'lyQod3RxJKlwxrz9SOpoelQM8kb4Mw9r',
    'tableId': 'QlP62Ie',
    'records': [{
        'recordId': 'XL18VolwuZ',
        'cells': {
            '0z3U03f': '优化后的商品名',
            'fXqD3tg': [{'url': '主图 URL'}],
            'fm413aQ': [{'url': '详情图 1 URL'}],
            'L46geMD': [{'url': '详情图 2 URL'}],
            'yjglrX6': [{'url': '详情图 3 URL'}],
            'sNF7cKY': [{'url': '详情图 4 URL'}],
            '2OxgAvb': [{'url': '详情图 5 URL'}]
        }
    }]
})
```

---

## 表格 Base 信息

- **Base ID**: `lyQod3RxJKlwxrz9SOpoelQM8kb4Mw9r`
- **表格链接**: https://alidocs.dingtalk.com/i/nodes/lyQod3RxJKlwxrz9SOpoelQM8kb4Mw9r
- **视图 ID**: `JVtrZeW`
- **Sheet ID**: `QlP62Ie`

---

## 字段验证规则

### 商品名字段验证

```python
def validate_product_name(name: str) -> bool:
    """验证商品名是否符合电商标准"""
    
    # 禁止出现标记词
    forbidden_words = ['Hot Search', 'Keyword Stuffing', 'Best Seller']
    if any(word in name for word in forbidden_words):
        return False
    
    # 必须包含品牌名
    brands = ['Blue Buffalo', 'Purina', 'Friskies', 'Fancy Feast']
    if not any(brand in name for brand in brands):
        return False
    
    # 长度限制 (亚马逊标题最大 200 字符)
    if len(name) > 200:
        return False
    
    # 必须是通顺的句子
    if not name[0].isupper() or name[-1] not in '.!)':
        return False
    
    return True
```

### 图片字段验证

```python
def validate_image_url(url: str) -> bool:
    """验证图片 URL 是否有效"""
    
    # 必须是 HTTP/HTTPS 链接
    if not url.startswith('http'):
        return False
    
    # 必须是图片格式
    image_extensions = ['.png', '.jpg', '.jpeg', '.webp']
    if not any(ext in url.lower() for ext in image_extensions):
        return False
    
    # 检查 URL 可访问性 (可选)
    try:
        response = requests.head(url, timeout=5)
        return response.status_code == 200
    except:
        return False
```

---

## 数据质量标准

### 完整性检查

- [] 所有必填字段已填写
- [] 优化后的商品名不为空
- [ ] 至少有一张主图
- [ ] 5 张详情图全部上传

### 准确性检查

- [ ] 商品名遵循标准结构
- [ ] 商品名不包含违规词汇
- [] 图片 URL 可正常访问
- [ ] 图片与商品匹配

### 一致性检查

- [ ] 同一商品的图片风格一致
- [ ] 主图选择整体场景
- [] 详情图具有场景差异
- [] 图片尺寸统一 (800x800)

---

## 常见问题

### Q: 附件字段写入失败？

**A**: 确保传入格式为 `[{"url": "..."}]`，而不是字符串或对象。

### Q: 商品名优化后太长？

**A**: 亚马逊标题限制 200 字符，超过会被截断。建议控制在 150 字符以内。

### Q: 图片生成失败？

**A**: 检查原始图片链接是否可访问，淘宝 MCP 需要能访问原图才能生成。

### Q: 如何批量更新多条记录？

**A**: `update_records` 支持一次更新多条记录，但建议每批不超过 100 条。

```python
# 批量更新示例
records_to_update = [
    {'recordId': 'rec1', 'cells': {...}},
    {'recordId': 'rec2', 'cells': {...}},
    # ... 最多 100 条
]
call_mcp_tool('update_records', {
    'baseId': base_id,
    'tableId': table_id,
    'records': records_to_update
})
```

---

**最后更新**: 2026-03-16
