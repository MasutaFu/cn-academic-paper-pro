# E. DOCX 生成与格式调优

C 刊投稿绝大多数要求 .docx 格式。本文件给出从纯文本到符合期刊体例的 .docx 的完整链路。

---

## 一、技术栈

主要工具：

- **pandoc**：markdown → docx 基础转换
- **python-docx**：docx 样式后处理
- **unzip / zip**：直接操作 docx 内部 XML（极端情况）

辅助：

- **LibreOffice**（headless 模式）：复杂转换
- **mammoth**：docx → markdown 反向转换

---

## 二、典型工作流

```
步骤 1：用 markdown 写正文（或从已有 docx 转出 markdown）
步骤 2：pandoc 转 markdown → docx
步骤 3：python-docx 后处理（字体、行距、缩进、页边距）
步骤 4：python-docx 处理特殊元素（脚注、参考文献样式）
步骤 5：输出到 /mnt/user-data/outputs/
步骤 6：present_files 交付
```

---

## 三、pandoc 基础转换

```bash
pandoc input.md -o output.docx
```

带参考样式：

```bash
pandoc input.md \
  --reference-doc=reference.docx \
  -o output.docx
```

`reference.docx` 是一个预设了字体、段落样式的模板文件。

### 3.1 markdown 输入的注意

- 一级标题用 `#`（对应 docx 的 Heading 1）
- 二级标题用 `##`（Heading 2）
- pandoc 默认会处理列表、表格、引用
- 数学公式可用 LaTeX 语法

---

## 四、python-docx 样式后处理（核心脚本）

```python
"""
中文学术论文 docx 格式化脚本
按《中国高教研究》体例调整字体、行距、缩进、页边距
"""

from docx import Document
from docx.shared import Pt, Cm, RGBColor
from docx.oxml.ns import qn
from docx.enum.text import WD_LINE_SPACING

def format_academic_docx(input_path, output_path):
    doc = Document(input_path)
    
    # ============ Normal 样式 ============
    style = doc.styles['Normal']
    
    # 字体设置
    font = style.font
    font.name = 'Times New Roman'  # 英文字体
    font.size = Pt(12)  # 小四号
    
    # 中文字体（东亚字体）
    rpr = style.element.get_or_add_rPr()
    rFonts = rpr.find(qn('w:rFonts'))
    if rFonts is None:
        rFonts = rpr.makeelement(qn('w:rFonts'), {})
        rpr.insert(0, rFonts)
    rFonts.set(qn('w:eastAsia'), 'SimSun')  # 宋体
    
    # 段落格式
    pf = style.paragraph_format
    pf.line_spacing = 1.5
    pf.first_line_indent = Cm(0.74)  # 2 字符缩进
    pf.space_after = Pt(0)
    pf.space_before = Pt(0)
    
    # ============ 标题样式 ============
    for heading_level in ['Heading 1', 'Heading 2', 'Heading 3']:
        if heading_level in [s.name for s in doc.styles]:
            hs = doc.styles[heading_level]
            hs.font.name = 'Times New Roman'
            hs.font.color.rgb = RGBColor(0, 0, 0)
            hs.font.bold = True
            
            hs_rpr = hs.element.get_or_add_rPr()
            hs_rFonts = hs_rpr.find(qn('w:rFonts'))
            if hs_rFonts is None:
                hs_rFonts = hs_rpr.makeelement(qn('w:rFonts'), {})
                hs_rpr.insert(0, hs_rFonts)
            hs_rFonts.set(qn('w:eastAsia'), 'SimHei')  # 黑体
            
            hs.paragraph_format.first_line_indent = Cm(0)
    
    # 标题字号
    if 'Heading 1' in [s.name for s in doc.styles]:
        doc.styles['Heading 1'].font.size = Pt(16)
    if 'Heading 2' in [s.name for s in doc.styles]:
        doc.styles['Heading 2'].font.size = Pt(14)
    if 'Heading 3' in [s.name for s in doc.styles]:
        doc.styles['Heading 3'].font.size = Pt(13)
    
    # ============ 页面设置 ============
    for section in doc.sections:
        section.top_margin = Cm(2.54)
        section.bottom_margin = Cm(2.54)
        section.left_margin = Cm(3.17)
        section.right_margin = Cm(3.17)
    
    doc.save(output_path)
    print(f"格式化完成：{output_path}")

if __name__ == "__main__":
    import sys
    format_academic_docx(sys.argv[1], sys.argv[2])
```

保存为 `format_academic.py`，调用：

```bash
python format_academic.py input.docx output.docx
```

---

## 五、特殊元素处理

### 5.1 参考文献节的样式

参考文献节通常字号小、单倍行距：

```python
# 找到参考文献节后的段落
ref_started = False
for para in doc.paragraphs:
    if '参考文献' in para.text and len(para.text) < 10:
        ref_started = True
        continue
    if ref_started:
        # 参考文献区的段落
        para.style = doc.styles['Normal']
        for run in para.runs:
            run.font.size = Pt(10.5)  # 五号字
        para.paragraph_format.line_spacing = 1.0
        para.paragraph_format.first_line_indent = Cm(0)
        para.paragraph_format.left_indent = Cm(0.74)  # 悬挂缩进
```

### 5.2 摘要节的样式

摘要节通常字号略小、左右缩进：

```python
for para in doc.paragraphs:
    if para.text.startswith('摘要：'):
        para.paragraph_format.left_indent = Cm(0.5)
        para.paragraph_format.right_indent = Cm(0.5)
        for run in para.runs:
            run.font.size = Pt(10.5)
```

### 5.3 表格样式

```python
for table in doc.tables:
    # 表格内字体
    for row in table.rows:
        for cell in row.cells:
            for para in cell.paragraphs:
                for run in para.runs:
                    run.font.size = Pt(10.5)
                    rpr = run._element.get_or_add_rPr()
                    rFonts = rpr.find(qn('w:rFonts'))
                    if rFonts is None:
                        rFonts = rpr.makeelement(qn('w:rFonts'), {})
                        rpr.insert(0, rFonts)
                    rFonts.set(qn('w:eastAsia'), 'SimSun')
```

### 5.4 首页脚注（基金 + 作者简介）

python-docx 对脚注支持有限。建议：

- 用 python-docx 在首页底部插入脚注
- 或在 pandoc 转换前用 markdown 的 `[^1]` 语法 + 脚注定义

复杂情况下直接编辑 .docx 内的 `word/footnotes.xml`：

```bash
unzip -p input.docx word/footnotes.xml > footnotes.xml
# 编辑后重新打包
zip input.docx word/footnotes.xml
```

---

## 六、本项目的特殊情况：docx 是纯文本

某些情况下，项目中的 .docx 文件实际是纯文本（伪 docx）：

```bash
# 判断
file input.docx
# 如果输出 "ASCII text" 或 "UTF-8 text"，是伪 docx

# 处理
cat input.docx  # 直接读
grep -n "..." input.docx  # 直接搜
```

### 6.1 伪 docx 转真 docx

```bash
pandoc input.docx -f markdown -o output.docx
```

或：

```python
from docx import Document
doc = Document()
with open('input.docx', 'r', encoding='utf-8') as f:
    text = f.read()
for line in text.split('\n'):
    doc.add_paragraph(line)
doc.save('output.docx')
```

### 6.2 真 docx 转纯文本（便于 grep）

```bash
pandoc input.docx -t plain --wrap=none > /tmp/text.txt
```

或：

```bash
unzip -p input.docx word/document.xml > /tmp/doc.xml
# 然后用 xmllint 或 python 解析
```

---

## 七、XML 直接操作（极端情况）

如果 python-docx 无法处理某些样式，直接操作 XML：

```bash
# 解包
mkdir docx_unpacked && cd docx_unpacked
unzip ../input.docx

# 编辑 word/document.xml、word/styles.xml 等

# 重新打包
zip -r ../output.docx .
```

### 7.1 常见 XML 操作

修改正文中的引用编号：

```python
import re

with open('word/document.xml', 'r', encoding='utf-8') as f:
    xml = f.read()

# 注意：& 字符在 XML 中是 &amp;
# 直接 replace 时要避免双重转义

old_to_new = {1: 5, 2: 3, 3: 8}  # 示例映射

# 用占位符避免重复替换
for old in old_to_new:
    xml = re.sub(rf'\[{old}\]', f'<<TEMP_{old}>>', xml)
for old, new in old_to_new.items():
    xml = xml.replace(f'<<TEMP_{old}>>', f'[{new}]')

# 修复双重转义
xml = xml.replace('&amp;amp;', '&amp;')

with open('word/document.xml', 'w', encoding='utf-8') as f:
    f.write(xml)
```

### 7.2 重新打包注意事项

```bash
cd docx_unpacked
# [Content_Types].xml 必须在首位
zip -X ../output.docx '[Content_Types].xml'
zip -rX ../output.docx . -x '[Content_Types].xml'
```

---

## 八、字体可用性

容器内可能没有中文字体，需要先安装：

```bash
# 检查
fc-list :lang=zh

# 如无，安装
apt-get install -y fonts-noto-cjk fonts-noto-cjk-extra
```

字体 fallback 链：

```
SimSun → Noto Serif CJK SC → DejaVu Serif（最后兜底）
SimHei → Noto Sans CJK SC Bold → DejaVu Sans Bold
```

---

## 九、输出与交付

### 9.1 输出路径

最终 docx 输出到：

```
/mnt/user-data/outputs/vNN-标题关键词.docx
```

### 9.2 交付

```python
# 用 present_files 工具
# 在代码中：
# present_files(['/mnt/user-data/outputs/vNN-...docx'])
```

### 9.3 同时输出 markdown 版本（可选）

某些情况下，markdown 版本便于后续编辑：

```bash
cp /home/claude/manuscript.md /mnt/user-data/outputs/vNN-标题.md
```

---

## 十、常见问题

### 10.1 pandoc 转换后字体丢失

原因：pandoc 使用默认字体，未应用模板。

修复：

```bash
pandoc input.md --reference-doc=template.docx -o output.docx
```

或转换后用 python-docx 后处理。

### 10.2 表格格式混乱

原因：markdown 表格语法限制（不支持合并单元格）。

修复：在 python-docx 中手工创建复杂表格，或直接编辑 XML。

### 10.3 引用编号显示异常

原因：pandoc 可能把 `[1]` 误识别为 markdown 引用。

修复：在 markdown 中用 `\[1\]` 转义，或转换后用 sed 替换。

### 10.4 中英文之间无空格

原因：python-docx 处理中文时不自动加空格。

修复：

```python
import re

for para in doc.paragraphs:
    new_text = re.sub(
        r'([\u4e00-\u9fa5])([a-zA-Z0-9])', r'\1 \2',
        para.text
    )
    new_text = re.sub(
        r'([a-zA-Z0-9])([\u4e00-\u9fa5])', r'\1 \2',
        new_text
    )
    if new_text != para.text:
        para.text = new_text  # 注意会丢失原 run 样式
```

---

## 十一、模板 docx 的准备

建议在容器内保留一份"模板 docx"，每次基于它生成：

```bash
# 在 /home/claude/templates/ 下放置
templates/
├── academic-cn-base.docx          # 通用 C 刊基础模板
├── academic-cn-zhongguogaojiao.docx  # 《中国高教研究》专用
├── academic-cn-gaodenggongcheng.docx # 《高等工程教育研究》专用
└── academic-cn-guanlishijie.docx    # 《管理世界》专用
```

每个模板预设：

- 字体（Normal 样式、各级标题）
- 段落格式（行距、缩进）
- 页面设置（页边距）
- 节标题编号样式

调用时：

```bash
pandoc input.md --reference-doc=templates/academic-cn-zhongguogaojiao.docx -o output.docx
```

---

## 十二、最后一步：人工检查

无论脚本多完美，最终交付前必须用 Word（或 WPS）打开 docx 人工检查：

- [ ] 字体显示正常
- [ ] 行距、缩进正确
- [ ] 标题层级正确
- [ ] 表格样式正常
- [ ] 图表位置正确
- [ ] 脚注 / 尾注正常
- [ ] 页码正常
- [ ] 中英文间距合适
- [ ] 标点全角 / 半角正确

脚本不能替代最后一遍肉眼。
