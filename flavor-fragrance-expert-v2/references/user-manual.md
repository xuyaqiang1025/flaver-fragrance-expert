# 香韵大师 — 用户操作手册

> 完整手册（含工作区说明）的主副本位于：  
> `E:\Data\Claude code\flavor-fragrance-expert\操作手册.md`  
> 本文与主副本保持同结构，供 Agent 在 Skill 内直接 Read。

---

## 1. 路径速查

| 类型 | 路径 |
|------|------|
| Skill | `C:\Users\PC\.agents\skills\flavor-fragrance-expert\` |
| 工作区根 | `E:\Data\Claude code\flavor-fragrance-expert\` |
| 用户记忆 | `...\ .flavor-memory\` |
| 领域库 | `...\ .flavor-domain\` |
| 交付 | `...\ deliveries\{香型英文}\{日期}_{简称}_{场景}_{模型}\` |

规范：`memory-system-spec.md`、`domain-knowledge-spec.md`、`html-report-spec.md`。

---

## 2. 启动

用户说出调香 / GC-MS / 仿香 / 合规 / 雾化等需求即触发本 Skill。按 Phase 0→6 执行；记忆与交付写入工作区写死路径（非当前 Cursor 打开的任意文件夹）。

---

## 3. 工作流要点

- **Phase 0**：分层读 PROFILE + INDEX + 近 1–3 档案；只调用生效 `[K0XX]`（合格 evidence）；禁用以候选指导设计；按需读 `[D0XX]`。
- **Phase 1**：确认需求；复述 PROFILE 请用户确认。
- **Phase 2–5**：解析 / 设计 / 合规 / 雾化。
- **Phase 6**：交付写入 `deliveries/.../`；规律默认进 `[KC0XX]`；列出候选问「哪些进知识库？」；仅用户确认后升格 `[K0XX]`；禁止擅自写领域库。

---

## 4. 编号与优先级

| 编号 | 库 | 设计时可调用 |
|------|-----|--------------|
| `[K0XX]` | 生效用户知识 | 是（过滤后） |
| `[KC0XX]` | 候选 | 否 |
| `[D0XX]` | 领域 | 按需 |

优先级：PROFILE → K → D → 当前输入 → 临场推理；合规版法规硬限制优先。

---

## 5. 用户常用口令（识别并执行）

- 升格：「KC00x 进库」「这条记住」「记进知识库」
- 拒绝：「候选全部拒绝」
- 领域导入：「写进领域库 / domain materials」
- 不落库：「只讨论，不要沉淀记忆」

---

## 6. 防污染（Agent 必守）

- `model-only` / 未验证 / 差模型试跑 → 只进候选
- 旧无元数据 K 条：兼容为暂可调用的 active + user-confirmed
- 香型目录英文 kebab-case
- 不把模型编造 CAS/OT 写入 `.flavor-domain` 且标 high

---

## 7. OT 说明（须向用户诚实说明）

报告中的 OT(ppb) 在未引用带 `source` 的领域原料卡时，**主要来自模型训练知识/估计**，不是用户自建实测库。  
OT 校正**方法**可用；具体数字应标估计/待核实，重要单体优先用领域库有出处数据。详见工作区 `操作手册.md` 第 10 节。

---

## 8. 交付检查清单

每次完整配方确认：

- [ ] `deliveries/{香型英文}/{日期}_{简称}_{场景}_{模型slug}/overview.md`
- [ ] 同目录 `report.md`、`report.html`
- [ ] INDEX 已登记交付路径与模型
- [ ] 新规律在候选库；已询问升格
- [ ] 输出中分列调用的 `[K0XX]` / `[D0XX]` 与新增 `[KC0XX]`
