# 香韵大师（flavor-fragrance-expert）操作手册

> 适用：Cursor 个人 Skill「香韵大师」  
> Skill 路径：`C:\Users\PC\.agents\skills\flavor-fragrance-expert\`  
> 工作区（记忆 / 领域 / 交付写死根目录）：`E:\Data\Claude code\flavor-fragrance-expert\`  
> 更新日期：2026-07-19

---

## 1. 这套系统是什么

资深调香师专家 Agent，面向**电子雾化香精**与**食用香精**，覆盖：

- GC-MS 解析、香韵结构重组、双版本配方（极致还原 / 合规落地）
- 雾化工程适配、MD + HTML 可视化交付
- **用户记忆**（越用越懂你，带候选/生效闸门）
- **垂类领域库**（原料 / 书摘 / 法规等行业通识，与用户记忆分离）

| 类型 | 路径 |
|------|------|
| Skill（行为规则） | `C:\Users\PC\.agents\skills\flavor-fragrance-expert\` |
| 用户记忆 | `E:\Data\Claude code\flavor-fragrance-expert\.flavor-memory\` |
| 领域知识 | `E:\Data\Claude code\flavor-fragrance-expert\.flavor-domain\` |
| 交付物 | `E:\Data\Claude code\flavor-fragrance-expert\deliveries\` |

详细规范（给 Agent 读）：

- Skill 内 `references/memory-system-spec.md` — 用户记忆与进库闸门
- Skill 内 `references/domain-knowledge-spec.md` — 领域库
- Skill 内 `references/html-report-spec.md` — HTML 报告
- 本文副本也在 Skill：`references/user-manual.md`

---

## 2. 怎么启动一次调香对话

1. 在 Cursor 新开对话（任意工作区均可；记忆与交付仍写入上表写死路径）。
2. 直接描述需求，例如：
   - 「用香韵大师解析这份 GC-MS，做中烘咖啡雾化配方」
   - 「帮我做 GB 41700 合规改造」
   - 「按上次记忆库里的咖啡偏好，做一版意式浓缩食用香精」
3. Agent 应按 Skill 执行 **Phase 0 → 1 → … → 6**。

也可点名：「按 flavor-fragrance-expert / 香韵大师 Skill 执行」。

---

## 3. 标准工作流（你需要配合的点）

### Phase 0：读记忆与领域（自动）

- 读取 `PROFILE.md`、`INDEX.md`、相关配方档案
- **只调用生效知识** `KNOWLEDGE.md` 中合格的 `[K0XX]`
- **不调用** `KNOWLEDGE-CANDIDATES.md` 指导设计
- 若存在 `.flavor-domain/`，按需检索原料 / 法规等 `[D0XX]`

### Phase 1：确认需求（需要你回答）

场景、香型、输入材料（GC-MS / 感官描述 / 现有配方）、输出版本等。  
若已有偏好档案，Agent 会复述，你确认或纠正即可。

### Phase 2–5：解析 → 设计 → 合规 → 雾化

产出完整配方分析；电子雾化场景会多出雾化适配章节。

### Phase 6：沉淀 + 交付（自动 + 你审核知识）

**交付（自动）** 写入：

```text
deliveries/{香型英文}/{YYYY-MM-DD}_{简称}_{场景}_{模型slug}/
  overview.md
  report.md
  report.html
```

示例：`deliveries/coffee/2026-07-16_1266甜焦糖_电子雾化_grok-4.5/`

**知识（有闸门，防污染）**：

1. 新规律默认进入 **候选库** → `[KC0XX]`（未验证 / 差模型试跑标 `evidence: model-only`）
2. 经你确认后才升格进 **生效库** → `[K0XX]`
3. Agent 应列出候选并询问：「哪些进知识库？」

你可以用这些口令：

- 「KC001、KC003 进知识库」
- 「把糠硫醇上限记住」
- 「候选全部不要 / 全部拒绝」

---

## 4. 三套编号（勿混用）

| 编号 | 位置 | 含义 | 设计时 Phase 0 |
|------|------|------|----------------|
| `[K0XX]` | `.flavor-memory/KNOWLEDGE.md` | 生效用户经验 | 可调用（合格 evidence） |
| `[KC0XX]` | `.flavor-memory/KNOWLEDGE-CANDIDATES.md` | 候选 | **不可调用** |
| `[D0XX]` | `.flavor-domain/` | 领域通识 | 可按需引用 |

**调用优先级**：

1. `PROFILE`（你的硬约束）  
2. 生效用户知识 `[K0XX]`  
3. 领域资料 `[D0XX]`  
4. 当前输入（GC-MS、当轮需求）  
5. 模型临场推理（缺口处应标「推测」）

**例外**：合规落地版中，**法规硬限制**优先于「极致还原」偏好。

---

## 5. 用户记忆库文件说明

路径：`.flavor-memory/`

| 文件 | 作用 | 更新方式 |
|------|------|----------|
| `PROFILE.md` | 偏好画像（场景、风格、合规、输出） | 合并更新；摘要最多约 20 行 |
| `KNOWLEDGE.md` | **生效**规律，影响下次设计 | 仅升格写入 |
| `KNOWLEDGE-CANDIDATES.md` | **候选**规律，默认不影响设计 | Phase 6 可自动追加 |
| `FEEDBACK-LOG.md` | 反馈时间线 + 验证状态 | 追加 |
| `INDEX.md` | 历史配方总览（含交付路径、模型、状态） | 追加 |
| `formulas/{香型英文}/` | 单次交互档案（决策摘要 + 链接交付） | 按次创建 / 迭代 |

香型目录必须用英文 kebab-case：`coffee`、`berry`、`tobacco`，**禁止** `咖啡` 等中文目录名。

配方状态建议：`待反馈` → `已反馈` → `已定版` / `废弃`。

---

## 6. 领域库怎么用

路径：`.flavor-domain/`

```text
.flavor-domain/
├── INDEX.md
├── materials/      # 香原料卡
├── formulas/       # 公开/经典骨架（不是你的 deliveries）
├── books/          # 书摘笔记
└── regulations/    # 法规摘要
```

| 动作 | 说明 |
|------|------|
| 导入资料 | 提供书摘 / 原料表 / 法规要点，并说「写进领域库」 |
| Agent 默认 | **只检索引用**，不擅自把模型编造的 CAS/OT 写成权威数据 |
| 与记忆区别 | 领域 = 懂行；记忆 = 懂你。编号用 `[D0XX]`，不走用户 K 升格流 |

---

## 7. 防污染原则（必记）

- 试跑、较差模型、未闻样验证 → 最多进**候选**，默认不进生效库  
- 不要轻易说「全部自动记进知识库」，除非接受污染风险  
- 旧 `KNOWLEDGE.md` 中缺少元数据的条目：按兼容规则暂仍可调用；建议逐步补 `status` / `evidence`  
- **禁止**把模型临场编造的原料数据自动写入 `.flavor-domain/`

---

## 8. 日常口令速查

| 你想做的事 | 可以说 |
|------------|--------|
| 新配方 | 「做一款…雾化/食用配方」 |
| 升格知识 | 「KC002 进库」「这条记住」 |
| 拒收候选 | 「候选全部拒绝」 |
| 导领域资料 | 「把下面原料卡写入 domain materials」 |
| 只咨询不落库 | 「只讨论，不要沉淀记忆」 |
| 查历史 | 「根据记忆库里上次咖啡配方…」 |
| 指定模型对比 | 交付目录名会带模型 slug，便于多模型对照 |

---

## 9. 交付物说明

每次完整配方应在对应交付目录得到：

| 文件 | 内容 |
|------|------|
| `overview.md` | 概览：分路、双版本对比、关键决策、使用建议 |
| `report.md` | 完整 MD 报告（极致还原 + 合规 + 分路 + 合规审查 + 雾化等） |
| `report.html` | 单文件 HTML（过程可视化 + 记忆调用展示） |

用浏览器打开 `report.html` 即可（内联 CSS，可离线）。

---

## 10. 关于 OT（嗅觉阈值）——重要说明

### 结论

**报告与历史记忆里出现的 OT（ppb）数值，在未接入带出处的领域原料卡之前，主要来自模型的训练知识 / 临场估计，不是你们自建的权威实测库。**

### 原因

1. Skill 要求「按 OT 做感官贡献校正」，但未强制「必须从某一份实测表读取」。
2. `.flavor-domain` 是后期增加的；规范明确禁止把模型编造的 CAS/OT 当作高置信写入领域库。
3. 现有 `.flavor-memory/KNOWLEDGE.md` 中的 OT 表述（如某单体「OT≈xxx ppb」）来源于历次交互沉淀，来源字段通常只有交互日期，**没有**文献或 `materials/*.md` 出处。

### 正确用法

| 层级 | 态度 |
|------|------|
| OT **校正方法**（用阈值校正峰面积贡献） | 方法论正确，继续使用 |
| 具体 OT **数字** | 视为参考估计；关键单体应用你们认可的手册 / 供应商 / 文献覆盖 |
| 领域库 `materials` | 维护「有 `source` 的 OT」→ 设计时优先引用 `[D0XX]` |
| 无出处 OT | 报告中宜标「估计 / 待核实」，且不要自动升格进生效 `KNOWLEDGE` |

---

## 11. 常见问题

**Q：换电脑或改盘符怎么办？**  
路径写死在 Skill 的 `SKILL.md` 与 `references/*-spec.md` 中，需同步修改三处（或以后改为可配置）。

**Q：为什么说「已沉淀」但下次设计好像没变？**  
多半只进了**候选库**；未升格为生效 `[K0XX]` 就不会进入 Phase 0 调用集。

**Q：可以把整本书丢进领域库吗？**  
建议只存摘要、表格与页码出处；整书原文既占空间也不利检索。

**Q：用户记忆和领域库冲突听谁的？**  
听用户已验证经验；但合规版必须遵守法规硬限制。

---

## 12. 推荐日常节奏

1. 提需求 → 确认场景与香型  
2. 收交付目录三文件 → 自己闻样 / 上机  
3. 回来反馈 → 更新验证状态  
4. 挑选候选「进知识库」  
5. （可选）把核实过的 OT / 限用表导入 `.flavor-domain/materials` 或 `regulations`

这样既能积累个人手感，又不会让试跑和模型估算污染生效库。
