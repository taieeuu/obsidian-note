---
Date: 2024-02-29
tags:
  - Python
---
# 基本用法
```python
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("echo", help="echo the string you use here")
args = parser.parse_args()
print(args.echo)

```

得到

```cmd
python prog.py -h
usage: prog.py [-h] echo

positional arguments:
  echo        echo the string you use here

options:
  -h, --help  show this help message and exit
```
# 參考文件
https://docs.python.org/zh-tw/3/library/argparse.html
https://docs.python.org/zh-tw/3/howto/argparse.html