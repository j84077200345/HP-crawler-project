

```python
import pandas as pd
import sqlite3
import plotly.plotly as py
import plotly.graph_objs as go
from plotly.graph_objs import Bar, Scatter, Marker, Layout

conn = sqlite3.connect("C:\Users\user\scrapy_project\googleplay\googleplay.sqlite")

#每個類別中，APP總數
df = pd.read_sql_query("select DISTINCT app_category, COUNT(*) as 'num_category' from googleplay GROUP BY app_category", conn)

trace1 = go.Bar(
    x = df['app_category'],
    y = df['num_category']
)

layout = dict(title = '每個類別中 APP總數',
              xaxis = dict(title = 'Category'),
              yaxis = dict(title = 'Number of App'))

data = [trace1]
fig = dict(data=data, layout=layout)

url_1 = py.plot(fig, filename='App_TotalCount', auto_open=False)
py.iplot(fig,filename='App_TotalCount')
```

    https://plot.ly/~j84077200345/44
    



<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~j84077200345/44.embed" height="525px" width="100%"></iframe>




```python
import plotly.plotly as py
import plotly.graph_objs as go

#每個類別中，APP的平均評價(APP樣本數量需多餘100才進行比較，否則會不准)
df = pd.read_sql_query("select app_category,AVG(star) AS avg from googleplay GROUP BY app_category HAVING COUNT(app_category)>1000", conn)

trace = go.Scatter(
    x = df['app_category'],
    y = df['avg'],
    mode = 'lines+markers',
    name = 'lines+markers'
)

layout = dict(title = '每個類別中 APP的星等平均分數',
              xaxis = dict(title = 'Category'),
              yaxis = dict(title = 'Star'))

data = [trace]
fig = dict(data=data, layout=layout)

url_2 = py.plot(fig, filename='App_AvgStar', auto_open=False)
py.iplot(fig,filename='App_AvgStar')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~j84077200345/42.embed" height="525px" width="100%"></iframe>




```python
import plotly.plotly as py
import plotly.graph_objs as go

#每個類別中，一個APP平均評分次數(評分總次數/APP數量)
df = pd.read_sql_query("select app_category,SUM(CAST(SUBSTR(star_people,6) as decimal))/COUNT(*) as comment from googleplay group by app_category order by comment", conn)

trace1 = go.Bar(
    x = df['app_category'],
    y = df['comment']
)

layout = dict(title = '每個類別中 APP評分次數的平均',
              xaxis = dict(title = 'Category'),
              yaxis = dict(title = 'Count'))

data = [trace1]
fig = dict(data=data, layout=layout)

url_3 = py.plot(fig, filename='App_Comment', auto_open=False)
py.iplot(fig,filename='App_Comment')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~j84077200345/46.embed" height="525px" width="100%"></iframe>




```python
import plotly.plotly as py
import plotly.graph_objs as go

#不同評價下APP總數(2顆星範圍是1.5~2.4)
df = pd.read_sql_query("select count(app_category) as num,round(star,0) as _star from googleplay group by _star order by _star", conn)

labels = df['_star']
values = df['num']
colors = ['#FEBFB3', '#E1396C', '#96D38C', '#D0F9B1']

trace = go.Pie(labels=labels, values=values,
               hoverinfo='label+percent', textinfo='value', 
               textfont=dict(size=20),
               marker=dict(colors=colors, 
                           line=dict(color='#000000', width=2)))
layout = dict(title = '不同星等評價下 APP的總數')
data = [trace]
fig = dict(data=data, layout=layout)

url_4 = py.plot(fig, filename='App_Star', auto_open=False)
py.iplot(fig, filename='App_Star')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~j84077200345/48.embed" height="525px" width="100%"></iframe>




```python
import plotly.plotly as py
import plotly.graph_objs as go

df1 = pd.read_sql_query("select count(title) as num, CAST(SUBSTR(other,16) as decimal) as _year from googleplay where app_category like '%教育%' group by _year", conn)
df2 = pd.read_sql_query("select count(title) as num, CAST(SUBSTR(other,16) as decimal) as _year from googleplay where app_category like '%娛樂%' group by _year having _year>2009", conn)
df3 = pd.read_sql_query("select count(title) as num, CAST(SUBSTR(other,16) as decimal) as _year from googleplay where app_category like '%工具%' group by _year HAVING _year >2009", conn)
df4 = pd.read_sql_query("select count(title) as num, CAST(SUBSTR(other,16) as decimal) as _year from googleplay where app_category like '%個人化%' group by _year HAVING _year >2009", conn)
df5 = pd.read_sql_query("select count(title) as num, CAST(SUBSTR(other,16) as decimal) as _year from googleplay where app_category like '%生活品味%' group by _year HAVING _year >2009", conn)

trace1 = go.Scatter(
    x = df1['_year'],
    y = df1['num'],
    mode = 'lines+markers',
    name = '教育(占9.7%)'
)
trace2 = go.Scatter(
    x = df1['_year'],
    y = df2['num'],
    mode = 'lines+markers',
    name = '娛樂(占7.8%)'
)
trace3 = go.Scatter(
    x = df1['_year'],
    y = df3['num'],
    mode = 'lines+markers',
    name = '工具(占6.4%)'
)
trace4 = go.Scatter(
    x = df1['_year'],
    y = df4['num'],
    mode = 'lines+markers',
    name = '個人化(占6.2%)'
)
trace5 = go.Scatter(
    x = df1['_year'],
    y = df5['num'],
    mode = 'lines+markers',
    name = '生活品味(占5.6%)'
)

layout = dict(title = 'TOP5_Class APP發佈數量 時間分析',
              xaxis = dict(title = 'Year'),
              yaxis = dict(title = 'Number of APP'))

data = [trace1,trace2,trace3,trace4,trace5]
fig = dict(data=data, layout=layout)

url_5 = py.plot(fig, filename='App_TOP5_Category', auto_open=False)
py.iplot(fig, filename='App_TOP5_Category')
```




<iframe id="igraph" scrolling="no" style="border:none;" seamless="seamless" src="https://plot.ly/~j84077200345/50.embed" height="525px" width="100%"></iframe>




```python
import plotly.dashboard_objs as dashboard
import plotly.plotly as py
import IPython.display
from IPython.display import Image

my_dboard = dashboard.Dashboard()

def fileId_from_url(url):
    
    """Return fileId from a url."""
    index = url.find('~')
    fileId = url[index + 1:]
    local_id_index = fileId.find('/')

    share_key_index = fileId.find('?share_key')
    if share_key_index == -1:
        return fileId.replace('/', ':')
    else:
        return fileId[:share_key_index].replace('/', ':')

def sharekey_from_url(url):
    """Return the sharekey from a url."""
    index = url.find('share_key=')
    return url[index + len('share_key='):]

fileId_1 = fileId_from_url(url_1)
fileId_2 = fileId_from_url(url_2)
fileId_3 = fileId_from_url(url_3)
fileId_4 = fileId_from_url(url_4)
fileId_5 = fileId_from_url(url_5)

box_a = {
    'type': 'box',
    'boxType': 'plot',
    'fileId': fileId_1,
    'title': 'app_totalcount'
}

box_b = {
    'type': 'box',
    'boxType': 'plot',
    'fileId': fileId_2,
    'title': 'app_avgstar',
    'shareKey': sharekey_from_url(url_2)
}

box_c = {
    'type': 'box',
    'boxType': 'plot',
    'fileId': fileId_3,
    'title': 'app_comment',
    'shareKey': sharekey_from_url(url_3)
}

box_d = {
    'type': 'box',
    'boxType': 'plot',
    'fileId': fileId_4,
    'title': 'app_star',
    'shareKey': sharekey_from_url(url_4)
}

box_e = {
    'type': 'box',
    'boxType': 'plot',
    'fileId': fileId_5,
    'title': 'top_5',
    'shareKey': sharekey_from_url(url_5)
}

my_dboard.insert(box_e)
my_dboard.insert(box_a, 'above', 1)
my_dboard.insert(box_b, 'right', 2)
my_dboard.insert(box_c, 'below', 3)
my_dboard.insert(box_d, 'above', 2)

my_dboard.get_box(1)['title'] = '時間分析'
my_dboard.get_box(2)['title'] = '星等分析'
my_dboard.get_box(3)['title'] = '類別星等分析'
my_dboard.get_box(4)['title'] = '評分次數分析'
my_dboard.get_box(5)['title'] = '類別分析'

my_dboard['settings']['title'] = 'Dashboard For Google_App with Python'
my_dboard['settings']['logoUrl'] = 'https://upload.wikimedia.org/wikipedia/commons/thumb/a/ad/HP_logo_2012.svg/1200px-HP_logo_2012.svg.png'

my_dboard['settings']['foregroundColor'] = '#000000'
my_dboard['settings']['backgroundColor'] = '#adcaea'
my_dboard['settings']['headerForegroundColor'] = '#ffffff'
my_dboard['settings']['headerBackgroundColor'] = '#F0BBFF'
my_dboard['settings']['boxBackgroundColor'] = '#ffffff'
my_dboard['settings']['boxBorderColor'] = '#000000'
my_dboard['settings']['boxHeaderBackgroundColor'] = '#ffffff'

py.dashboard_ops.upload(my_dboard, 'Dashboard For Google_App with Python')
```




    u'https://plot.ly/~j84077200345/53/dashboard-for-google-app-with-python/'




```python

```
