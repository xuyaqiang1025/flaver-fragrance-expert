# 垂类领域知识库规范 (Domain Knowledge Specification)

> 存放调香**行业通识**：香原料数据、书摘笔记、经典/公开配方骨架、法规摘要等。
> 与用户记忆库 `.flavor-memory/` **严格分离**。编号使用 `[D0XX]`，**禁止**与用户 `[K0XX]` / `[KC0XX]` 混用。

---

## 一、设计目标

1. **懂行**：为配方提供可核查的原料、法规与文献底座，降低幻觉
2. **可维护**：由用户导入/修订资料；Agent 默认只检索引用，不擅自写入
3. **按需加载**：按香型/CAS/关键词打开少量文件，禁止整库塞入上下文
4. **信任可排序**：领域通识优先级低于用户已验证经验，法规硬限制除外

---

## 二、目录结构

**根路径写死**（与记忆库同工作区）：

`E:\Data\Claude code\flavor-fragrance-expert\.flavor-domain\`

```
.flavor-domain\
├── INDEX.md                 # 资料总览与检索入口
├── materials\               # 香原料卡（一料一文件或分类汇总）
│   ├── _template.md
│   └── ...
├── formulas\                # 经典/公开配方骨架（非用户 deliveries）
│   ├── _template.md
│   └── ...
├── books\                   # 书摘与笔记（按书分册）
│   └── ...
└── regulations\             # 法规/限用摘要
    ├── _template.md
    └── ...
```

首次交互时：若不存在则创建上述空目录与 `INDEX.md` 骨架；**不**自动填充虚构原料数据。

---

## 三、编号与元数据

- 领域条目编号：`[D001]`、`[D002]`… 全局递增
- 每条（或每张卡）建议字段：

| 字段 | 说明 |
|------|------|
| `type` | material / book / formula / regulation |
| `source` | 书名+页码 / SDS / 国标条款 / 自建笔记日期 |
| `updated` | YYYY-MM-DD |
| `tags` | coffee, ester, sulfur, gb41700… |
| `confidence` | high（有权威出处）/ medium / low（笔记存疑） |
| `cas` | 原料类必填（若可知） |

无 `source` 的内容不得标 `confidence: high`；Agent 不得把模型编造的 CAS/OT 写成 high。

---

## 四、各子库模板

### 4.1 materials — 香原料卡

文件名建议：`{cas或英文名}.md`（kebab-case）

```markdown
# [D0XX] 原料名（中英文）

- type: material
- cas: XX-XX-X
- tags: ...
- source: ...
- updated: YYYY-MM-DD
- confidence: high

## 感官
- 气味描述：...
- OT（ppb，若可知）：...
- 典型用途/香型：...

## 理化（若可知）
- 沸点 / LogP / 溶解度（PG/VG）：...

## 合规与安全
- 食用（GB 2760 等）：允许 / 限量 / 禁用 / 未知
- 雾化吸入风险：...
- 操作注意：...

## 用量经验（通识，非用户偏好）
- 文献/行业常用范围：...
```

### 4.2 formulas — 参考骨架

```markdown
# [D0XX] 配方骨架名称

- type: formula
- tags: coffee, ...
- source: ...
- updated: YYYY-MM-DD
- confidence: medium

## 适用场景
...

## 分路/骨架摘要
...

## 关键单体与大致比例（通识）
| 组分 | CAS | 参考用量 | 说明 |
|------|-----|---------|------|

> 非用户量产配方；实战交付仍以 deliveries/ 为准。
```

### 4.3 books — 书摘

```markdown
# [D0XX] 《书名》— 章节/主题

- type: book
- source: 《书名》p.xx
- tags: ...
- updated: YYYY-MM-DD
- confidence: medium

## 摘要要点
1. ...
2. ...

## 可行动启发
- ...
```

只存摘要与笔记，不整书粘贴。

### 4.4 regulations — 法规摘要

```markdown
# [D0XX] 法规要点标题

- type: regulation
- source: GB 41700 / GB 2760 / TPD / ...
- tags: compliance, e-liquid, ...
- updated: YYYY-MM-DD
- confidence: high

## 适用范围
...

## 关键限制（摘要）
| 物质/类别 | 要求 | 备注 |
|----------|------|------|

## 配方设计含义
- ...
```

---

## 五、INDEX.md 骨架

```markdown
# 领域知识库索引

> 最后更新：YYYY-MM-DD | 条目约计：N

## 快速检索
| ID | 类型 | 标题 | 路径 | tags |
|----|------|------|------|------|
| D001 | material | 2-糠硫醇 | materials/98-02-2.md | coffee, sulfur |

## 按类型
### materials
- ...
### regulations
- ...
```

---

## 六、读取与写入规则

### 6.1 读取（Phase 0 / Phase 3）

1. 先读 `.flavor-domain/INDEX.md`（若存在）
2. 按当前香型、单体、合规场景用 tags/CAS **点名打开** 3–10 个相关文件
3. 引用时标注 `[D0XX]` 与 `source`
4. 与用户 `[K0XX]` 冲突时：跟用户生效知识；合规硬限制跟法规 `[D0XX]`

### 6.2 写入（默认禁止自动）

| 动作 | 是否允许 |
|------|----------|
| 用户说「把这张原料卡写进领域库」 | 允许，按模板写入并更新 INDEX |
| 用户拖入/提供书摘、表格要求归档 | 允许 |
| Agent 自行把模型生成的 OT/CAS 写入 materials | **禁止** |
| 将用户 deliveries 配方复制进 domain/formulas | **禁止**（那是用户记忆/交付） |

### 6.3 与用户记忆的边界

| | `.flavor-memory` | `.flavor-domain` |
|--|------------------|------------------|
| 内容 | 你的偏好与已验证规律 | 行业资料 |
| 编号 | K / KC | D |
| 进库 | 候选→用户确认→生效 | 用户导入/明确要求 |
| Phase 0 | 必读 PROFILE + 生效 K | 按需读 |

---

## 七、维护

- 法规与限用表定期核对出处日期；过时条目在 INDEX 标注 `outdated`
- 原料卡优先维护高频指纹单体
- 不在领域库存放个人隐私或供应商密钥
