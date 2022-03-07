---
title: Dash 的基础回调
tags:
  - Python
id: '73'
categories:
  - - uncategorized
date: 2021-03-10 21:03:09
---

官方文档：[Basic Dash Callbacks](https://dash.plotly.com/basic-callbacks)

## 最简单的交互式 Dash 应用

```python
@app.callback(
    Output(component_id='my-output', component_property='children'),
    Input(component_id='my-input', component_property='value')
)
def update_output_div(input_value):
    return 'Output: {}'.format(input_value)
```

输入输出作为 `@app.callback` 装饰器的参数。

`component_id` 代表页面中组件的 ID，`component_property` 代表组件中的具体参数。

当 `Input` 指定的参数发生变化时，装饰器所装饰的函数会被立即调用，变化后的参数将成为函数的传入参数。函数的返回值将被输出到 `Output` 所指定的参数上。

`Input` 必须在 `Output` 之后。

## 具有多个输入和多个输出的情况

```python
@app.callback(
    Output('square', 'children'),
    Output('cube', 'children'),
    Output('twos', 'children'),
    Output('threes', 'children'),
    Output('x^x', 'children'),
    Input('num-multi', 'value'))
def callback_a(x):
    return x**2, x**3, 2**x, 3**x, x**x
```

```python
@app.callback(
    Output('display-selected-values', 'children'),
    Input('countries-radio', 'value'),
    Input('cities-radio', 'value'))
def set_display_children(selected_country, selected_city):
    return u'{} is a city in {}'.format(
        selected_city, selected_country,
    )
```

在装饰器中放入多个 `Output` 和 `Input` 即可，同时函数需要有对应数量的传入参数和返回值，顺序对应。

## `State` 的运用

```python
@app.callback(Output('output-state', 'children'),
              Input('submit-button-state', 'n_clicks'),
              State('input-1-state', 'value'),
              State('input-2-state', 'value'))
def update_output(n_clicks, input1, input2):
    return u'''
        The Button has been pressed {} times,
        Input 1 is "{}",
        and Input 2 is "{}"
    '''.format(n_clicks, input1, input2)
```

`State` 与 `Input` 类似，一样可让回调函数取到组件的参数值，区别在于当 `State` 指定的数据变动时回调函数不会被调用，适合构建表单式的页面。

`State` 参数必须在 `Output` 和 `Input` 之后。