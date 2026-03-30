# balabababa - 综合科研数据库检索

> AI助手请先阅读本文档

## 用途

本目录包含多个科研数据库的检索技能(skill)，AI根据用户需求选择对应数据库执行查询。

## 数据库列表

| 数据库 | Skill文件 | 用途 |
|--------|-----------|------|
| NCBI SRA | `NCBI/NCBIdb.md` | 重测序/WGS原始测序数据检索 |
| RiceGeneFunction | `RiceGeneFunction/ricegenefunction.md` | 水稻基因功能注释查询 |
| ArabidopsisGeneFunction | `ArabidopsisGeneFunction/arabidopsisgenefunction.md` | 拟南芥基因功能注释查询 |

## 使用方式

```
1. 阅读本文档了解可用数据库
2. 根据用户意图选择对应数据库
3. 加载对应 .md 文件执行查询
4. 重要的是遇到404访问不了还是要继续在本框架内继续尝试，不要轻易跳出框架
```

## AI选择逻辑

| 用户意图 | 选择数据库 | Skill文件 |
|---------|-----------|-----------|
| 想获取原始测序数据(SRR/reads) | NCBI SRA | `NCBI/NCBIdb.md` |
| 查询水稻基因(Os格式)功能 | RiceGeneFunction | `RiceGeneFunction/ricegenefunction.md` |
| 查询拟南芥基因(AT格式)功能 | ArabidopsisGeneFunction | `ArabidopsisGeneFunction/arabidopsisgenefunction.md` |

### 快速判断

- **SRR/重测序/WGS** → NCBI SRA
- **Os01g... ~ Os12g... / LOC_Os...** → RiceGeneFunction  
- **AT1G... ~ AT5G...** → ArabidopsisGeneFunction

## 详细文档

如需更多指导规则，查看 `Databaseinfo.md`
