# 🐍 Python Toolkit

> 实用Python脚本合集。自动化、文件处理、数据处理、日常工具。

## 📦 脚本列表

| 脚本 | 功能 | 
|------|------|
| `file_organizer.py` | 按类型自动整理文件夹 |
| `batch_rename.py` | 批量重命名文件 |
| `csv_to_json.py` | CSV转JSON |
| `image_resizer.py` | 批量调整图片大小 |
| `text_counter.py` | 统计文本字数/词频 |
| `password_gen.py` | 随机密码生成器 |
| `qr_generator.py` | 二维码生成器 |
| `web_scraper.py` | 简单网页爬虫 |
| `pdf_merger.py` | PDF合并工具 |
| `markdown_to_html.py` | Markdown转HTML |
| `json_tool.py` | JSON格式化/校验/压缩 |

## 🚀 快速开始

```bash
git clone https://github.com/cjtdawn-cn/python-toolkit.git
cd python-toolkit
python3 file_organizer.py --help
```

每个脚本独立运行，无外部依赖（除标注外）。

---

## 1. file_organizer.py — 自动整理文件夹

```python
#!/usr/bin/env python3
"""按文件类型自动分类整理文件夹"""

import os
import shutil
from pathlib import Path

CATEGORIES = {
    "图片": [".jpg", ".jpeg", ".png", ".gif", ".bmp", ".svg", ".webp"],
    "文档": [".pdf", ".doc", ".docx", ".txt", ".md", ".xlsx", ".pptx"],
    "视频": [".mp4", ".avi", ".mov", ".mkv", ".wmv"],
    "音频": [".mp3", ".wav", ".flac", ".aac", ".ogg"],
    "压缩包": [".zip", ".rar", ".7z", ".tar", ".gz"],
    "代码": [".py", ".js", ".html", ".css", ".java", ".cpp", ".go"],
}

def organize(path):
    path = Path(path)
    for file in path.iterdir():
        if file.is_file():
            ext = file.suffix.lower()
            for category, extensions in CATEGORIES.items():
                if ext in extensions:
                    dest = path / category
                    dest.mkdir(exist_ok=True)
                    shutil.move(str(file), str(dest / file.name))
                    print(f"  {file.name} → {category}/")
                    break

if __name__ == "__main__":
    import sys
    target = sys.argv[1] if len(sys.argv) > 1 else "."
    organize(target)
    print("Done!")
```

---

## 2. batch_rename.py — 批量重命名

```python
#!/usr/bin/env python3
"""批量重命名文件 — 加前缀、后缀、序号"""

import os
import sys
from pathlib import Path

def rename(path, prefix="", suffix="", digits=3):
    path = Path(path)
    files = sorted([f for f in path.iterdir() if f.is_file()])
    for i, file in enumerate(files, 1):
        ext = file.suffix
        new_name = f"{prefix}{str(i).zfill(digits)}{suffix}{ext}"
        new_path = path / new_name
        file.rename(new_path)
        print(f"  {file.name} → {new_name}")

if __name__ == "__main__":
    target = sys.argv[1] if len(sys.argv) > 1 else "."
    prefix = sys.argv[2] if len(sys.argv) > 2 else "file_"
    rename(target, prefix=prefix)
```

---

## 3. qr_generator.py — 二维码生成器

```python
#!/usr/bin/env python3
"""生成二维码图片"""
# pip install qrcode[pil]

import qrcode
import sys

text = sys.argv[1] if len(sys.argv) > 1 else input("输入内容: ")
filename = sys.argv[2] if len(sys.argv) > 2 else "qr.png"

img = qrcode.make(text)
img.save(filename)
print(f"已保存: {filename}")
```

---

## 4. password_gen.py — 密码生成器

```python
#!/usr/bin/env python3
"""随机生成安全密码"""

import secrets
import string

def generate(length=16, upper=True, digits=True, symbols=True):
    chars = string.ascii_lowercase
    if upper: chars += string.ascii_uppercase
    if digits: chars += string.digits
    if symbols: chars += "!@#$%^&*"
    return ''.join(secrets.choice(chars) for _ in range(length))

if __name__ == "__main__":
    import sys
    n = int(sys.argv[1]) if len(sys.argv) > 1 else 5
    for i in range(n):
        print(generate(20))
```

---

## 5. text_counter.py — 字数统计

```python
#!/usr/bin/env python3
"""统计文本文件字数、行数、词频"""

import sys
from collections import Counter
import re

def count(filepath):
    with open(filepath, 'r', encoding='utf-8') as f:
        text = f.read()
    
    chars = len(text)
    lines = text.count('\n') + 1
    words = len(re.findall(r'\w+', text))
    
    # Top 10 words
    word_freq = Counter(re.findall(r'\w+', text.lower())).most_common(10)
    
    print(f"文件: {filepath}")
    print(f"字符数: {chars:,}")
    print(f"行数: {lines:,}")
    print(f"单词数: {words:,}")
    print(f"\n高频词:")
    for word, freq in word_freq:
        print(f"  {word}: {freq}")

if __name__ == "__main__":
    count(sys.argv[1])
```

---

## 6. json_tool.py — JSON工具

```python
#!/usr/bin/env python3
"""JSON格式化/校验/压缩工具 — 管道友好，无外部依赖"""

import sys
import json

def pretty(data, indent=2):
    return json.dumps(data, indent=indent, ensure_ascii=False)

def compact(data):
    return json.dumps(data, separators=(',', ':'), ensure_ascii=False)

def extract(data, key):
    keys = key.split('.')
    for k in keys:
        if isinstance(data, list):
            try:
                data = data[int(k)]
            except (IndexError, ValueError):
                return f"Error: index '{k}' out of range"
        else:
            data = data.get(k)
            if data is None:
                return f"Error: key '{k}' not found"
    return json.dumps(data, indent=2, ensure_ascii=False)

if __name__ == "__main__":
    import argparse
    parser = argparse.ArgumentParser(description="JSON工具：格式化/校验/压缩/提取")
    parser.add_argument('file', nargs='?', default='-', help='JSON文件 (默认stdin)')
    parser.add_argument('-c', '--compact', action='store_true', help='压缩为单行')
    parser.add_argument('-t', '--indent', type=int, default=2, help='缩进空格数 (默认2)')
    parser.add_argument('-k', '--key', help='提取指定键值 (如 "users.0.name")')
    parser.add_argument('-v', '--validate', action='store_true', help='仅校验JSON')
    args = parser.parse_args()

    try:
        src = sys.stdin if args.file == '-' else open(args.file, 'r', encoding='utf-8')
        raw = src.read()
        data = json.loads(raw)
        if isinstance(src, type(sys.stdin)):
            pass
        else:
            src.close()
    except json.JSONDecodeError as e:
        print(f"❌ JSON解析错误: {e}", file=sys.stderr)
        sys.exit(1)
    except FileNotFoundError:
        print(f"❌ 文件未找到: {args.file}", file=sys.stderr)
        sys.exit(1)

    if args.validate:
        print(f"✅ 有效JSON ({len(raw):,} bytes)")
    elif args.key:
        print(extract(data, args.key))
    elif args.compact:
        print(compact(data))
    else:
        print(pretty(data, args.indent))
```

**用法：**
```bash
cat data.json | python json_tool.py              # 格式化
echo '{"a":1}' | python json_tool.py -c          # 压缩
cat config.json | python json_tool.py -k server.port  # 提取
cat data.json | python json_tool.py -v           # 校验
```

---

## 📄 License

MIT © [Cao Jiangtao](https://github.com/cjtdawn-cn)

---

> 💡 更多实用开发工具 → [AI Toolkit Zone](https://cjtdawn-cn.github.io/ai-toolkit-zone/)
