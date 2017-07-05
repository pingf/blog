---
title: dash学习笔记1
date: 2017-07-05 14:54:01
tags: [python,dash,plotly]
author: dameng
---

最近实验了一下plotly出品的dash, 相当不错, 正是俺们想要的.
简单来说, 就是一个可以不用写html,js的纯python展示框架.

# 安装

```python
pip install dash==0.17.7  # The core dash backend
pip install dash-renderer==0.7.3  # The dash front-end
pip install dash-html-components==0.6.2  # HTML components
pip install dash-core-components==0.5.3  # Supercharged components
pip install plotly==2.0.12  # Plotly graphing library used in examples
```

可以看到dash的不同部分被拆散到不同的包中去咯.
dash 为我们提供了两种组件, core组件和html组件, core组件为ploty相关的图表组件, html为基础的div, p这样的组件.

# 布局

```python
# -*- coding: utf-8 -*-
import dash
import dash_core_components as dcc
import dash_html_components as html

app = dash.Dash()

app.layout = html.Div(children=[
    html.H1(children='Hello Dash'),

    html.Div(children='''
        Dash: A web application framework for Python.
    '''),

    dcc.Graph(
        id='example-graph',
        figure={
            'data': [
                {'x': [1, 2, 3], 'y': [4, 1, 2], 'type': 'bar', 'name': 'SF'},
                {'x': [1, 2, 3], 'y': [2, 4, 5], 'type': 'bar', 'name': u'Montréal'},
            ],
            'layout': {
                'title': 'Dash Data Visualization'
            }
        }
    )
])

if __name__ == '__main__':
    app.run_server(debug=True)
```

简化后, app的布局可以写成如下的样子

```python
Div([
    H1('Hello Dash'),

    Div(Dash: A web application framework for Python'),

    Graph(
        id='example-graph',
        figure={
			....
        }
    )
])
```


# 交互

上面的例子并不具备交互性, 如果我们想要使用一个按钮去触发某个图表,需要使用callback装饰器(早期的版本叫react装饰器)

```python
import dash
from dash_core_components import Graph
from dash_html_components import Div, H1, Button
import random

app = dash.Dash()

def gen(i):
    return {'x': [1, 2, 3], 'y': [random.randint(1, 10) for i in range(3)], 'type': 'bar', 'name': str(i)}

app.layout =Div(children=[
    H1('Hello Dash'),

    Div('''
        Dash: A web application framework for Python.
    '''),

    Graph(
        id='graph',
        figure={
            'data': [gen(i) for i in range(3)],
            'layout': {
                'title': 'Dash Data Visualization'
            }
        }
    ),
    Div(children=[
            Button('update', id='update')
        ],
        style={'textAlign': 'right'}
    )
])

@app.callback(
    dash.dependencies.Output(component_id='graph', component_property='figure'),
    events=[dash.dependencies.Event(component_id='update', component_event='click')])
def update():
    figure={
	'data': [gen(i) for i in range(3)],
	'layout': {
	    'title': 'Dash Data Visualization'
	}
    }
    return figure

if __name__ == '':
    app.run_server(debug=True)
```

callback 有几个参数, output, inputs, state(为啥这个不是states呢), events. 除了output其它都是接受列表.
return的值应该和output中component_property中的值对应. 这里只展示如何处理button的click事件, 不作过多的展开.
另外例子中的注意style的设置方式.


# 展示效果

[![展示效果](/content/images/hellodash.png)](/content/images/hellodash.png)

