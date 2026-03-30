# Rice Gene Function Query Skill

## Description
从 RiceData 水稻基因数据库 (https://www.ricedata.cn/gene/) 查询水稻基因的功能注释信息。支持单基因查询和批量查询。

## Prerequisites
1. 确保 agent-browser 已安装：`npm install -g agent-browser`
2. 查看 agent-browser 用法：`agent-browser --help`
3. 如首次使用，运行：`agent-browser install`

## When to use
- 用户提供基因号（如 Os03g0856500、Os04g0446401）时调用
- 查询水稻基因的名称或功能注释信息
- 批量查询多个基因的功能注释

## Parameters
- `gene_list`: 水稻基因号列表，格式如 `Os03g0856500`, `Os04g0446401` 等
- 支持 Os01g... 到 Os12g... 格式的基因号
- 也支持 LOC_Os... 格式的 MSU Locus ID

---

## Workflow - 单基因查询

### Step 1: 打开数据库并获取页面元素
```bash
agent-browser open https://www.ricedata.cn/gene/
agent-browser wait --load networkidle
agent-browser snapshot -i
```

**预期元素引用：**
- 搜索框: `@e11`
- 搜索按钮: `@e14`

> **注意**: 每次重新打开页面或页面结构变化时，需要重新执行 `agent-browser snapshot -i` 获取最新的元素引用。

### Step 2: 执行查询
```bash
agent-browser fill @e11 "Os04g0446401"
agent-browser click @e14
agent-browser wait --load networkidle
```

### Step 3: 获取结果
```bash
agent-browser snapshot -i
```

**关键元素引用（结果表格）：**
- `@e63`: GeneID（数字ID）
- `@e64`: 基因功能注释（Annotation）
- `@e65`: RAP_Locus（Os格式基因号）
- `@e66`: MSU_Locus（LOC_Os格式）

### Step 4: 提取信息
从 snapshot 输出中查找包含 `ref=e64` 的行，例如：
```
- cell "lipase class 3 family protein, putative, expressed" [ref=e64]
```

从该行提取引号内的功能描述文本。

---

## Workflow - 批量查询

当需要查询多个基因时，使用以下流程：

### Step 1: 打开数据库
```bash
agent-browser open https://www.ricedata.cn/gene/
agent-browser wait --load networkidle
agent-browser snapshot -i
```

### Step 2: 获取初始元素引用
记录搜索框 `@e11` 和搜索按钮 `@e14` 的引用。

### Step 3: 循环查询每个基因

对每个基因执行以下操作：

```bash
# 3.1 填充基因号
agent-browser fill @e11 "{gene_id}"

# 3.2 点击搜索
agent-browser click @e14

# 3.3 等待页面加载（重要！）
agent-browser wait --load networkidle

# 3.4 获取快照查看结果
agent-browser snapshot -i
```

### Step 4: 从结果中提取注释

从 snapshot 输出中查找基因功能注释：
- 搜索包含 `ref=e64` 的行
- 该行的格式为：`- cell "{功能注释文本}" [ref=e64]`
- 提取引号内的文本即为基因功能注释

**示例输出：**
```
- cell "lipase class 3 family protein, putative, expressed" [ref=e64]
```

**提取结果：** `lipase class 3 family protein, putative, expressed`

### Step 5: 继续下一个基因
重复 Step 3，无需重新获取元素引用（除非出现错误）。

---

## 结果解析指南

### 表格结构
```
共1条记录, 1/1页
GeneID | 基因名称或注释 | 基因符号 | RAP_Locus | MSU_Locus或其它 | NCBI_Locus | cDNAs | RefSeq_Locus | Uniprots
```

### 关键字段说明
| 字段 | 说明 | 示例 |
|------|------|------|
| GeneID | 数据库内部ID | 21510 |
| 基因名称或注释 | 基因功能描述 | lipase class 3 family protein, putative, expressed |
| RAP_Locus | RAP-DB基因号 | Os04g0509100 |
| MSU_Locus | MSU-DB基因号 | LOC_Os04g43030 |

### 常见注释类型
| 注释关键词 | 含义 |
|-----------|------|
| putative, expressed | 推测表达的蛋白 |
| hypothetical protein | 假设蛋白，功能未知 |
| disease resistance | 疾病抗性相关 |
| transcription factor | 转录因子 |
| expressed protein | 表达蛋白（功能待定） |

---

## Error Handling

### 问题1: 元素引用失效
**症状**: `Could not locate element` 错误

**解决方案**:
```bash
agent-browser snapshot -i
```
重新获取页面元素引用，然后继续。

### 问题2: 页面加载超时
**症状**: 命令执行后长时间无响应

**解决方案**:
```bash
agent-browser wait --load networkidle
```
手动等待页面加载完成。

### 问题3: 基因未找到
**症状**: 页面显示 "共0条记录"

**说明**: 该基因号在数据库中不存在，可能是基因号错误或格式不对。

### 问题4: 结果表格元素位置变化
**症状**: 无法找到 @e64 等元素

**解决方案**:
```bash
agent-browser snapshot
```
获取完整快照，从结果区域查找包含 "cell" 和基因信息的行。

---

## 完整批量查询示例

### 准备基因列表
```
Os04g0446401
Os04g0447400
Os04g0453400
Os04g0478000
Os04g0509100
```

### 执行查询
```bash
# 1. 打开数据库
agent-browser open https://www.ricedata.cn/gene/
agent-browser wait --load networkidle

# 2. 查询第一个基因
agent-browser fill @e11 "Os04g0446401"
agent-browser click @e14
agent-browser wait --load networkidle
# 查看结果，记录 @e64 的内容

# 3. 查询第二个基因
agent-browser fill @e11 "Os04g0447400"
agent-browser click @e14
agent-browser wait --load networkidle
# 查看结果

# ... 继续查询其他基因
```

### 批量查询 Python 脚本模板
```python
import subprocess
import time

genes = [
    "Os04g0446401",
    "Os04g0447400", 
    "Os04g0453400",
    # 添加更多基因...
]

def query_gene(gene_id):
    # 填充基因号
    subprocess.run(["agent-browser", "fill", "@e11", gene_id], capture_output=True)
    time.sleep(0.3)
    
    # 点击搜索
    subprocess.run(["agent-browser", "click", "@e14"], capture_output=True)
    time.sleep(2)
    
    # 获取快照
    result = subprocess.run(["agent-browser", "snapshot"], capture_output=True, text=True)
    
    # 解析结果（查找 ref=e64 的行）
    annotation = "Not found"
    for line in result.stdout.split('\n'):
        if 'ref=e64' in line and 'StaticText' in line:
            # 提取功能注释
            pass
    
    return annotation

for gene in genes:
    result = query_gene(gene)
    print(f"{gene}\t{result}")
```

---

## 返回结果格式

### 单基因结果
```json
{
  "gene_id": "Os04g0509100",
  "annotation": "lipase class 3 family protein, putative, expressed",
  "gene_id_db": 21510,
  "msu_locus": "LOC_Os04g43030"
}
```

### 批量结果（表格格式）
| 基因ID | 功能注释 |
|--------|----------|
| Os04g0446401 | Conserved hypothetical protein |
| Os04g0447400 | glutamate decarboxylase, putative, expressed |
| Os04g0453400 | transporter family protein, putative, expressed |

---

## Notes

1. **数据库URL**: https://www.ricedata.cn/gene/
2. **支持格式**: 
   - Os01g... 到 Os12g... (RAP-DB格式)
   - LOC_Os... (MSU格式)
3. **结果语言**: 功能注释为英文描述
4. **等待时间**: 每次查询后建议等待 2-3 秒让页面完全加载
5. **元素引用**: 页面刷新或结构变化时需要重新获取
6. **网络要求**: 需要稳定的网络连接访问 RiceData 数据库

## 常见基因功能分类

| 类别 | 注释关键词示例 |
|------|---------------|
| 抗病相关 | disease resistance, resistance protein |
| 转录因子 | transcription factor, WRKY, MADS |
| 酶类 | lipase, oxidase, synthase, kinase |
| 蛋白转运 | transporter, channel, antiporter |
| 核糖体蛋白 | ribosomal protein |
| 信号转导 | calmodulin, leucine-rich repeat |
| 代谢相关 | dehydrogenase, transferase |
| 未知功能 | hypothetical protein, expressed protein |
