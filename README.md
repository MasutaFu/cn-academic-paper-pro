# cn-academic-paper-pro

中文 C 刊学术论文从零起稿到投稿定稿的全流程写作 agent。

---

## 安装

将整个目录放入 `/mnt/skills/user/` 或 Claude 可访问的 skills 路径：

```
/mnt/skills/user/cn-academic-paper-pro/
├── SKILL.md
├── PROJECT-CONTEXT.md  （可选：项目级补充）
└── references/
    ├── 01-topic-framing.md
    ├── 02-theory-framework.md
    ├── 03-literature-review.md
    ├── 04-methodology.md
    ├── 05-drafting.md
    ├── 06-revision-loop.md
    ├── 07-submission-prep.md
    ├── A-style-guide.md
    ├── B-references-gbt7714.md
    ├── C-political-compliance.md
    ├── D-figure-style.md
    ├── E-docx-output.md
    ├── F-self-review.md
    ├── G-ai-detection.md
    ├── H-banned-words.md
    └── I-banned-words-by-paragraph-position.md
```

---

## 文件功能速查

### 主入口

| 文件 | 功能 |
|---|---|
| `SKILL.md` | 总入口，定义触发条件、阶段地图、风格底盘 |
| `PROJECT-CONTEXT.md` | IC 人才论文项目专属知识库（其他论文跳过）|

### 阶段性方法论（按论文生命周期）

| 文件 | 阶段 | 何时加载 |
|---|---|---|
| `references/01-topic-framing.md` | 选题立项 | 用户说"想写一篇关于 X 的论文" |
| `references/02-theory-framework.md` | 理论框架 | 用户问"理论框架怎么搭" |
| `references/03-literature-review.md` | 文献综述 | 用户问"文献综述怎么写" |
| `references/04-methodology.md` | 方法设计 | 用户问"方法论""比较研究" |
| `references/05-drafting.md` | 初稿撰写 | 用户说"帮我写""起草" |
| `references/06-revision-loop.md` | 迭代修订 | 用户说"改一改""审稿意见怎么回应" |
| `references/07-submission-prep.md` | 投稿适配 | 用户说"投稿""期刊格式" |

### 横切手册（任何阶段都可能调用）

| 文件 | 用途 |
|---|---|
| `references/A-style-guide.md` | 去 AI 化与学术语体（最常用）|
| `references/B-references-gbt7714.md` | 参考文献体系管理 |
| `references/C-political-compliance.md` | 涉台、跨国比较等政治措辞合规 |
| `references/D-figure-style.md` | 学术配图规范 |
| `references/E-docx-output.md` | docx 生成与格式调优 |
| `references/F-self-review.md` | 自评清单与多专家审稿模板 |
| `references/G-ai-detection.md` | CNKI AIGC 检测应对 |
| `references/H-banned-words.md` | 硬禁用词与密度管理词清单 |
| `references/I-banned-words-by-paragraph-position.md` | 按段落位置的敏感词清单 |

---

## 典型工作流

### 工作流 1：从零写一篇新论文

```
用户：我想写一篇关于 X 的论文

Claude（按 skill）：
1. 加载 01-topic-framing.md
2. 问：目标期刊？核心问题？现有材料？
3. 完成立项 → 加载 02 + 03 + 04
4. 完成理论 / 文献 / 方法节 → 加载 05
5. 进入初稿撰写
6. 初稿完成 → 加载 06 + F + A
7. 迭代直到稳定
8. 投稿前 → 加载 07 + E + B
```

### 工作流 2：审稿意见回应

```
用户：审稿意见来了，怎么改

Claude（按 skill）：
1. 加载 06-revision-loop.md
2. 读审稿意见，按 P0/P1/P2 拆解
3. 输出修改清单（不动文件）
4. 等用户确认
5. 执行修改
6. 输出新版 + 修订总账
```

### 工作流 3：去 AI 化

```
用户：知网检测 AI 率会有多少 / 看看哪里还有 AI 痕迹

Claude（按 skill）：
1. 加载 A + G + H + I
2. 跑扫描脚本（三层）
3. 按区域风险输出修改清单
4. 优先处理结语、政策启示、引言第一段
5. 重写而非微调
```

### 工作流 4：参考文献体系化

```
用户：参考文献编号乱了 / 引而未列

Claude（按 skill）：
1. 加载 B-references-gbt7714.md
2. 跑 check_refs.py 诊断
3. 按 GB/T 7714-2015 重编号
4. 同时更新正文引用
5. 验证：无缺号、无引而未列、按首次出现顺序
```

---

## 核心特性

### 1. 不让 Claude 假装动手

skill 强制要求：审稿先于动稿，每个修改建议必须给可直接替换的文本，重大改动前必须等用户确认。

### 2. 不让 Claude 编造文献

不确定信息明确标"可能不准确，建议核实"。参考文献必经 CNKI / Google Scholar 验证。

### 3. 不让 Claude 输出彩色 AI 风格图

学术配图严格要求黑白灰 + 直角矩形 + 细线条 + 宋体配黑体，禁用 Claude 默认 visualize 工具的彩色圆角风格。

### 4. 多层 AI 痕迹防护

```
第一层：词汇（H-banned-words.md）
第二层：句式（A-style-guide.md）
第三层：结构（A + I）
```

按 **结构 → 句式 → 词汇** 顺序处理，不本末倒置。

### 5. 完整的政治合规

涉台、涉港澳、跨国比较的所有典型场景都有标准用语对照。

---

## 与其他 skill 的关系

本 skill **加载并覆盖** `cn-academic-paper`（通用中文学术论文 skill）。

执行时序：

```
触发 cn-academic-paper-pro
  ↓
view cn-academic-paper SKILL.md（取通用基础规则）
  ↓
本 skill 为最终裁定（冲突时本 skill 优先）
```

---

## 维护与扩展

### 适合您的情况：继续修改本 skill

- 写完一篇新论文，发现某条规则需要更新 → 修改对应 references
- 出现新的术语决议 → 写入 PROJECT-CONTEXT.md 或新建项目级 context
- 发现新的 AI 痕迹模式 → 补充 A 和 I

### 适合扩展的方向

- `references/J-quantitative-research.md`（如有量化研究需求）
- `references/K-policy-report-mode.md`（政策报告写作模式）
- `references/L-english-journal-conversion.md`（中→英投稿转换）
- 不同期刊的专属 reference-doc.docx 模板

---

## 字数与结构

| 文件类型 | 文件数 | 总字数 |
|---|---|---|
| SKILL.md | 1 | ~13000 |
| 阶段性 references (01-07) | 7 | ~60000 |
| 横切 references (A-I) | 9 | ~83000 |
| PROJECT-CONTEXT.md | 1 | ~5000 |
| **合计** | **18** | **~161000** |

主 SKILL.md 约 268 行，符合 skill-creator 的 < 500 行建议。
各 reference 文件均在 200-520 行之间，结构化清晰。

---

## 适用范围

✅ **适合**：
- 中文 C 刊学术论文撰写
- 教育学、科技政策、比较制度、公共管理等社科领域
- 投稿《中国高教研究》《高等工程教育研究》《教育研究》《管理世界》等
- 从零起稿到投稿定稿的全流程
- 多轮审稿与修订

❌ **不适合**：
- 自然科学领域论文（化学、物理、生物等需领域特化）
- 海外英文期刊投稿（按目标期刊编辑政策）
- 学位论文（结构、字数、审稿机制不同，需另写 skill）
- 政策报告（已有 policy-analyst skill 更适配）
- 课程作业（杀鸡用牛刀）

---

## 致谢

本 skill 基于 masterfoo（清华大学教育学院/UNESCO ICEE）撰写论文的数百轮迭代经验提炼。所有规则均来自实战，不是理论。
