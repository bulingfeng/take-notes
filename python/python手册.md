当mac使用pip安装东西的时候不成功，但是又使用pip3来继续安装的时候，mac分不清你是使用brew按照还是pip安装。使用以下命令来切换python的环境。

```
python3 -m venv path/to/venv
source path/to/venv/bin/activate
```

