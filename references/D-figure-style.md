# D. 学术配图规范

C 刊正文插图的核心审美是 **朴素、克制、信息密度高、看不出 AI 痕迹**。本文件给出从设计到输出的完整规范。

---

## 一、核心审美原则

参照《中国高教研究》《教育研究》《管理世界》等中文 C 刊的正文插图风格：

- 黑白灰为主色调
- 直角矩形，无圆角
- 细线条
- 朴素字体（宋体、黑体）
- 紧凑布局，留白合理但不浪费

判断标准：**该图能否直接出现在 C 刊正文中而不显突兀？**

---

## 二、色彩规范

### 2.1 允许的颜色

- 纯黑 `#000000`：线条、关键文字
- 深灰 `#333333`-`#555555`：次级文字、辅助线
- 中灰 `#888888`：虚线、分隔线
- 浅灰 `#F0F0F0`-`#F5F5F5`：节点背景填充（区分层次）

### 2.2 禁用的颜色

- 任何彩色调色板：teal、coral、purple、amber、青绿、珊瑚红等 AI 工具默认色
- 渐变色
- 阴影
- 高光
- 发光效果
- "暖色"或"冷色"主题色

### 2.3 类别区分的替代方案

不用彩色区分类别，改用：

- 线型：实线 / 虚线 / 点线
- 填充：空心 / 浅灰 / 深灰
- 形状：矩形 / 圆形 / 菱形
- 位置：左 / 中 / 右、上 / 下 分组

---

## 三、形状与线条

### 3.1 矩形

- 一律直角矩形，禁用圆角（rx > 0）
- 边框线宽 0.7-1.0 pt
- 主干框线 0.85-1.0 pt
- 次级框线 0.7-0.8 pt

### 3.2 箭头

- 形状：细长三角形
- matplotlib 用 `arrowstyle='-|>'`、`mutation_scale=7-10`
- 避免粗壮、装饰性箭头
- 实线箭头：主导逻辑关系
- 虚线箭头：反向、补充或观测关系

### 3.3 辅助线

- 分层线：细虚线（0.4-0.6 pt）
- 不用大块色带或装饰条分层

### 3.4 禁用形状元素

- 手绘感线条
- 圆角曲线装饰
- 流动感线条
- 阴影投影
- 3D 立体效果

---

## 四、字体规范

### 4.1 中文字体

- 正文（节点内文字）：宋体（Noto Serif CJK SC 或 SimSun）
- 标题、框内主标签：黑体（Noto Sans CJK SC Bold 或 SimHei）

### 4.2 英文字体

- 正文：Times New Roman
- 标题：等线体（sans-serif 不带装饰）

### 4.3 字号

| 用途 | 字号 |
|---|---|
| 框内主标题 | 10-10.5 pt |
| 次级文字 | 9-9.5 pt |
| 注释、辅助说明 | 8.5 pt |

### 4.4 加粗

- 仅用于框内核心概念标签
- 正文说明、注释不加粗

---

## 五、版式布局

### 5.1 整体风格

参考的期刊：

- 《中国高教研究》正文插图
- 《教育研究》正文插图
- 《管理世界》理论框架图
- American Economic Review 的理论框架图
- Research Policy 的概念图

### 5.2 分层方式

- 不用大块色带分层
- 用左侧窄栏标签（如"分析层""变量层"）
- 或用竖向虚线区分

### 5.3 文字方向

- 横排为主
- 避免斜排、竖排艺术字效果

### 5.4 留白

- 图形元素之间留白合理
- 不浪费空间
- 紧凑但不拥挤

---

## 六、图注与编号

### 6.1 图题格式

```
图 X　[图名]
```

- 全角空格分隔编号与图名
- 无西文句号
- 位于图下方居中

### 6.2 注释

```
注：实线箭头表示从政策工具到组织治理、再到培养机制的主导分析顺序；
虚线箭头表示实践中培养安排与组织结构对政策工具选择的反向影响；
底部虚线箭头表示三组解释变量对整个链条运作差异的解释关系。
```

- 以"注："引出
- 说明线型含义、缩写全称、数据来源等技术信息
- 图中若出现缩写或专有名词，首次出现处用括号给出全称

---

## 七、输出规格

### 7.1 双格式输出

每张图同时输出：

- **PNG**（400 dpi 以上，用于 Word 文档嵌入）
- **PDF** 矢量格式或 **SVG**（用于期刊排版系统）

### 7.2 尺寸

- 单栏插图：约 8-9 cm 宽
- 跨栏插图：约 16-18 cm 宽

### 7.3 文件命名

```
fig1_framework.png
fig1_framework.pdf
fig2_coding_matrix.png
fig2_coding_matrix.pdf
```

英文小写、下划线、便于期刊投稿系统处理。

---

## 八、生成方法

### 8.1 Python matplotlib（推荐）

```python
import matplotlib.pyplot as plt
import matplotlib.patches as mpatches
from matplotlib.patches import FancyArrowPatch, Rectangle
import matplotlib as mpl

# 字体设置
mpl.rcParams['font.sans-serif'] = ['Noto Sans CJK SC', 'SimHei']
mpl.rcParams['font.serif'] = ['Noto Serif CJK SC', 'SimSun']
mpl.rcParams['axes.unicode_minus'] = False

fig, ax = plt.subplots(figsize=(9, 5.5), dpi=400)
ax.set_xlim(0, 10)
ax.set_ylim(0, 6)
ax.axis('off')

# 直角矩形（无圆角）
rect = Rectangle((1, 4), 2, 1, linewidth=0.9, edgecolor='black',
                 facecolor='#F0F0F0')
ax.add_patch(rect)
ax.text(2, 4.5, '政策工具组合', ha='center', va='center',
        fontfamily='Noto Sans CJK SC', fontsize=10, fontweight='bold')

# 细长箭头（实线）
arrow = FancyArrowPatch((3, 4.5), (5, 4.5),
                        arrowstyle='-|>', mutation_scale=10,
                        linewidth=0.9, color='black')
ax.add_patch(arrow)

# 虚线箭头（反向）
arrow_dash = FancyArrowPatch((5, 4.3), (3, 4.3),
                              arrowstyle='-|>', mutation_scale=8,
                              linewidth=0.7, linestyle='--', color='#333333')
ax.add_patch(arrow_dash)

# 保存
plt.savefig('/home/claude/fig1_framework.png', dpi=400,
            bbox_inches='tight', facecolor='white')
plt.savefig('/home/claude/fig1_framework.pdf',
            bbox_inches='tight', facecolor='white')
```

### 8.2 不用的工具

- **Mermaid**：默认风格圆角、彩色，不符合 C 刊审美
- **visualize 工具**：默认 diagram 模板有彩色圆角分层，明显 AI 痕迹
- **draw.io / Lucidchart**：默认导出风格不符合期刊

如必须用上述工具，需手工调整每个元素的样式，反而更费时。直接 matplotlib 更可控。

---

## 九、常见图类型的规范

### 9.1 框架图 / 概念图

- 节点矩形 + 箭头连接
- 同层节点尺寸一致
- 主流箭头实线，反向虚线

### 9.2 流程图

- 起止节点用圆角矩形（仅此例外，象征流程起止）
- 中间节点用直角矩形
- 决策节点用菱形

### 9.3 编码矩阵图

- 灰度可视化（0 / 1 / 2 用三档灰度）
- 表格化呈现
- 无装饰边框

### 9.4 数据图（柱状图、折线图）

- 单色或灰度
- 不用彩色区分系列（用线型或填充模式）
- 网格线最浅
- 字号控制紧凑

---

## 十、绘图前的自检

每张图绘制前问自己：

- [ ] 图传达的核心信息是什么？一句话说清楚
- [ ] 是否需要图？文字能否说清？（能就不画）
- [ ] 信息密度是否合适？（不过密不过疏）
- [ ] 是否能在 A4 纸上看清所有文字？
- [ ] 是否符合本节所有规范？

---

## 十一、绘图后的自检

每张图绘制后检查：

- [ ] 黑白灰配色
- [ ] 无圆角矩形
- [ ] 字体宋体 + 黑体
- [ ] 线宽 0.7-1.0 pt
- [ ] 实线 / 虚线区分逻辑
- [ ] 图题格式正确
- [ ] 注释清晰
- [ ] PNG + PDF 双格式输出
- [ ] 文件命名规范

最后做"AI 痕迹测试"：把图发给一位熟悉学术出版的朋友看，问他能否一眼看出是 AI 生成的。如能，重画。

---

## 十二、紧急情况：必须用现成工具时

如时间紧迫必须用 visualize 或类似工具：

1. 选最简单的模板
2. 删除所有装饰元素
3. 改色为黑白灰
4. 改圆角为直角
5. 字体改为宋体 / 黑体
6. 输出后再用 matplotlib / Inkscape 后期清理

但这是下策，**推荐直接 matplotlib 一次到位**。
