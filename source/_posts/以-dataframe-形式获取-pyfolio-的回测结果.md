---
title: 以 DataFrame 形式获取 Pyfolio 的回测结果
tags:
  - Python
id: '78'
categories:
  - - uncategorized
date: 2021-03-13 16:52:46
---

在做项目时用到了 Pyfolio 这个库，在 Jupyter Notebook 里调用它的 `create_returns_tear_sheet` 方法时会以 HTML 的形式显示一个包含众多技术指标的表格和许多其它图表。但我希望以 DataFrame 形式得到那个关于技术指标的表格。

查看 `create_simple_tear_sheet` 函数的源码，我发现这个表格是由 `plotting.show_perf_stats` 函数完成的，函数中的 `perf_stats` 即为我需要的那个表格，函数最后将 `perf_stats` 传入 `utils.print_table` 这个用于绘制 HTML 表格的函数，将这一步修改为 `return perf_stats` 即可。