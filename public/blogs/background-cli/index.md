# 背景
最近在跑一个工业场景的rag系统建库流程，我需要用SSH远程连接服务器访问算力，同时还需要大量的建库时间，而SSH的问题在于连接一中断，我的任务就会被系统回收。因此，必须要让建库流程在后台稳定进行，不受SSH连接问题的干扰。

# nohup + & + 日志重定向

最常用的命令是：
```{python}
nohup 命令 > 日志文件 2>&1 &

nohup python script.py > run.log 2>&1 &
```

而其中*&* 是让进程后台运行的核心！

例如我本次执行的命令：
```{python}
nohup /path/to/python build_kb_from_dir.py \
  --pdf_dir /tmp/standard_mix_batch50 \
  --working_dir /path/to/workdir \
  --max_files 50 > run.log 2>&1 &
```

- \ 代表换行符号
- --pdf_dir /tmp/standard_mix_batch50    前者表示参数， 后者表示参数值
- > run.log 2>&1 表示把标准输出和错误都保存到 run.log 文件中，方便查看进度。