## 项目版本
本项目用到的Python版本为3.7.9，如果出现不兼容情况，请尝试切换Python版本

|**项目依赖**|**安装指令**|**项目使用版本**|
|:----:|:--------:|:--------:|
|**mitmproxy**|**pip install mitmproxy==5.3.0**|**5.3.0**|

## 简述对象用途

|**对象名字**|**类型**|**作用**|
|:----:|:--------:|:--------:|
|**Request**|**HTTP请求类**|**发起request**|
|**Response**|**HTTP响应类**|**接收response**|
|**Message**|**HTTP信息类**|**处理HTTP信息**|
|**MultiDictView**|**字典工具类**|**可迭代方便后续处理**|

## 官方HTTP模板
>**以下是官方的[HTTP事件模板](https://docs.mitmproxy.org/archive/v5/addons-events/#http-events)<br>不过常用的只有request和response事件<br>所以我们要对模板删减一部分**

```python
"""HTTP-specific events."""
import mitmproxy.http


class Events:
    def http_connect(self, flow: mitmproxy.http.HTTPFlow):
        """
            An HTTP CONNECT request was received. Setting a non 2xx response on
            the flow will return the response to the client abort the
            connection. CONNECT requests and responses do not generate the usual
            HTTP handler events. CONNECT requests are only valid in regular and
            upstream proxy modes.
        """

    def requestheaders(self, flow: mitmproxy.http.HTTPFlow):
        """
            HTTP request headers were successfully read. At this point, the body
            is empty.
        """

    def request(self, flow: mitmproxy.http.HTTPFlow):
        """
            The full HTTP request has been read.
        """

    def responseheaders(self, flow: mitmproxy.http.HTTPFlow):
        """
            HTTP response headers were successfully read. At this point, the body
            is empty.
        """

    def response(self, flow: mitmproxy.http.HTTPFlow):
        """
            The full HTTP response has been read.
        """

    def error(self, flow: mitmproxy.http.HTTPFlow):
        """
            An HTTP error has occurred, e.g. invalid server responses, or
            interrupted connections. This is distinct from a valid server HTTP
            error response, which is simply a response with an HTTP error code.
        """
```

## 启动Mitmproxy
>**我们根据官方插件开发的[教程](https://docs.mitmproxy.org/archive/v5/addons-overview/#anatomy-of-an-addon)<br>**
>>|**名称**|**命名要求**|**类型**|**用途**|
>>|:----:|:--------:|:--------:|:--------:|
>>|**Counter**|**任意命名**|**class**|**插件实例**|
>>|**addons**|**必须为addons**|**list**|**记录多个插件(任务列表)**|

>**新建`addons.py`文件并写入以下内容<br>**

```python
import mitmproxy.http
from mitmproxy import ctx

class Counter:
  
    def request(self, flow: mitmproxy.http.HTTPFlow):

        ctx.log.info('白色标准输出:{}'.format(flow.request.url))
        ctx.log.warn('黄色警告输出:{}'.format(flow.request.url))
        ctx.log.error('红色异常输出:{}'.format(flow.request.url))

    def response(self, flow: mitmproxy.http.HTTPFlow):

        print(flow.response.status_code)#获取网页状态码
        
addons = [
    Counter()
]
```

>**随后在终端输入`mitmweb -s addons.py`启动插件<br>**
>**显示以下内容说明成功启动mitmproxy<br>**
>**自动弹出`mitmweb`默认自带管理界面`http://127.0.0.1:8081`<br>**

```bash
Web server listening at http://127.0.0.1:8081
Loading script addons.py
Proxy server listening at http://*:8080
```

## 连接Mitmproxy

>**安装requests模块，用来发起HTTP请求并连接`8080`端口**
```bash
pip install requests
```
>**新建`http_demo.py`文件并写入以下内容**
```python
import requests

headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.141 Safari/537.36'
}

proxies = {
    'http':'127.0.0.1:8080',
    'https':'127.0.0.1:8080',
}

url = 'https://www.baidu.com'

html = requests.get(url,headers=headers,proxies = proxies,verify=False)
print(html.status_code)
```
>**浏览器访问`http://127.0.0.1:8081`进入`mitmweb`管理界面<br>**
>**在终端输入`python http_demo.py`运行Python文件<br>**
>**此时`mitmweb`管理界面就会出现抓包内容<br>**