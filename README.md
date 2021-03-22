# **Mitmproxy-Document**
## **[哔哩哔哩作者：-相依-](https://space.bilibili.com/343154012)** &emsp;**UPDATE 2021-3-22**

> **本项目为大家提供一个Mitmproxy中文文档**<br>
>  - **[🔥Mitmproxy视频教学](https://www.bilibili.com/video/BV1Lv411a7CP)**
>  - **[⚡国内优先 Gitee 在线阅读](https://mochazi.gitee.io/mitmproxy-doc-cn)** | **[Gitee 仓库地址](https://gitee.com/mochazi/mitmproxy-doc-cn)**
>  - **[🎉国外优先 GitHub 在线阅读](https://mochazi.github.io/mitmproxy-doc-cn)** | **[GitHub 仓库地址](https://github.com/mochazi/mitmproxy-doc-cn)**
>>**由于mitmproxy[官方API文档说明](https://docs.mitmproxy.org/archive/v5/addons-overview/#addons)，从v3版本开始就没有规范好API手册，只能通过pydoc查看最新代码规范。<br>所以小弟整理了与HTTP相关的API帮助大家处理抓包问题，本次翻译的是v5.3.0官方API文档。<br>如果我翻译或理解错了，请尝试查看旧版[v2.0.2官方API文档](https://docs.mitmproxy.org/archive/v2/index.html#document-scripting/api)，感谢您的支持！**
>>>**该项目仅限学习交流，请勿用于商业用途，如有侵权，请联系删除。**

<h1>目录</h1>

* **[运行环境](#environment)**
* **[启动Mitmproxy](#start-mitmproxy)**
* **[连接Mitmproxy](#connect-mitmproxy)**
* **[简述对象用途](#resume)**
* **[Request对象](#request)**
    * **[Request对象属性](#request-attribute)**
    * **[Request对象方法](#request-method)**
* **[Response对象](#response)**
    * **[Response对象属性](#response-attribute)**
    * **[Response对象方法](#response-method)**
* **[Message对象](#message)**
    * **[Message对象属性](#message-attribute)**
    * **[Message对象方法](#message-method)**
* **[MultiDictView对象](#multi-dict-view)**
    * **[MultiDictView对象方法](#multi-dict-view-method)**
* **[官方英文文档](#english-document)**

***

<h1 id="environment">运行环境</h1>

>**本项目用到的Python版本为3.7.9，如果出现不兼容情况，请尝试切换Python版本**
>>|**项目依赖**|**安装指令**|**项目使用版本**|
>>|:----:|:--------:|:--------:|
>>|**mitmproxy**|**pip install mitmproxy==5.3.0**|**5.3.0**|

***

<h1 id="start-mitmproxy">启动Mitmproxy</h1>

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
>**我们根据官方插件开发的[教程](https://docs.mitmproxy.org/archive/v5/addons-overview/#anatomy-of-an-addon)<br>**
>|**名称**|**命名要求**|**类型**|**用途**|
>|:----:|:--------:|:--------:|:--------:|
>|**Counter**|**任意命名**|**class**|**插件实例**|
>|**addons**|**必须为addons**|**list**|**记录多个插件(任务列表)**|

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
***

<h1 id="connect-mitmproxy">连接Mitmproxy</h1>

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

***

<h1 id="resume">简述对象用途</h1>

>|**对象名字**|**类型**|**作用**|
>|:----:|:--------:|:--------:|
>|**Request**|**HTTP请求类**|**发起request**|
>|**Response**|**HTTP响应类**|**接收response**|
>|**Message**|**HTTP信息类**|**处理HTTP信息**|
>|**MultiDictView**|**字典工具类**|**可迭代方便后续处理**|

***

<h1 id="request">Request对象</h1>
<h2 id="request-attribute">Request对象属性</h2>

>**假如你的URL为：https://xxx.com/xxx/xxx**
>>|**Request对象属性**|**作用**|**例子**|
>>|:----:|:--------:|:--------:|
>>|**`flow.request`**|**获取mitmproxy.http.HTTPRequest对象**|**返回对象**|
>>|**`flow.request.port`**|**获取request端口号**|**返回端口号**|
>>|**`flow.request.url`**|**获取request的url**|**返回https://xxx.com**|
>>|**`flow.request.pretty_url`**|**获取request的pretty_url<br>和普通url很类似<br>但这个主要用于<br>mitmproxy的透明模式**|**直接返回url**|
>>|**`flow.request.host`**|**获取request的host**|**返回<br>xxx.com**|
>>|**`flow.request.pretty_host`**|**获取当前request的pretty_host<br>和host_header很类似<br>但这个主要用于<br>mitmproxy的透明模式**|**返回<br>xxx.com**|
>>|**`flow.request.host_header`**|**获取当前request的<br>host_header此属性是根据<br>headers的host进行映射的**|**返回<br>xxx.com**|
>>|**`flow.request.method`**|**获取request的method**|**返回<br>GET、POST等等**|
>>|**`flow.request.scheme`**|**获取request的scheme**|**返回<br>http、https等等协议**|
>>|**`flow.request.path`**|**获取request的path**|**返回<br>URL的/xxx/xxx**|
>>|**`flow.request.multipart_form`**|**获取request的多部分表单数据**|**返回<br>MultiDictView对象**|
>>|**`flow.request.path_components`**|**获取request的url的路径(元组形式)**|**返回元组**|
>>|**`flow.request.urlencoded_form`**|**获取当前request的url编码表单数据**|**返回<br>MultiDictView对象**|
>>|**`flow.request.first_line_format`**|**根据[HTTP的规范](https://www.rfc-editor.org/rfc/rfc7230.txt)<br>origin-form和asterisk-form<br>将被判断为relative(相对的)**|**返回str**|

***
<h2 id="request-method">Request对象方法</h2>

>**假如你的URL为：https://xxx.com**
>>|**Request对象方法**|<div style="width:300px">作用</div>|**例子**|
>>|:-------:|:----------:|:-----------:|
>>|**`flow.request.text.replace(self, pattern, repl, flags=0, count=0)`**|**替换request的内容<br>pattern：被替换的内容(正则表达式模式)<br>repl：替换的内容<br>flags：标记数量<br>count：替换次数**|**常用于`text.replace('xxx','xxx')`<br>与get_text()或text属性配合使用即可**|
>>|**`flow.request.make(method: str, url: str, content: Union[bytes, str] = '', headers: Union[mitmproxy.net.http.headers.Headers, Dict[Union[str, bytes], Union[str, bytes]], Iterable[Tuple[bytes, bytes]]] = ()) -> 'Request'`**|**生成新的request的<br>method、url、content、headers等等<br>用于修改或替换request<br>主要用途：捉弄别人**|**返回Request对象<br>直接使用<br>`flow.request.make(method='GET',url='https://xxx.com',headers={'xxx':'xxx'})`**|
>>|**`flow.request.anticache(self)`**|**删除request可能产生的缓存<br>删除了协议头的ETags和If-Modified-Since**|**直接`anticache()`即可**|
>>|**`flow.request.anticomp(self)`**|**删除request可能压缩数据资源的标头**|**直接`anticomp()`即可**|
>>|**`flow.request.constrain_encoding(self)`**|**限制request的Accept-Encoding值<br>方便我们之后解码**|**直接`constrain_encoding()`即可**|

***
<h1 id="response">Response对象</h1>
<h2 id="response-attribute">Response对象属性</h2>


>|**Response对象属性**|**作用**|**例子**|
>|:----:|:--------:|:--------:|
>|**`flow.response.status_code`**|**获取response的状态码**|**直接`status_code`即可**|
>|**`flow.response.cookies`**|**获取response的cookies**|**返回MultiDictView对象<br>直接`flow.response.cookies`即可**|
>|**`flow.response.reason`**|**获取response的reason<br>注意：HTTP/2不包括reason**|**返回MultiDictView对象<br>直接`flow.response.cookies`即可**|

***
<h2 id="response-method">Response对象方法</h2>

>|**Response对象方法**|<div style="width:300px">作用</div>|**例子**|
>|:----:|:--------:|:--------:|
>|**`flow.response.text.replace(self, pattern, repl, flags=0, count=0)`**|**替换response的内容<br>pattern：被替换的内容(正则表达式模式)<br>repl：替换的内容<br>flags：标记数量<br>count：替换次数**|**常用于text.replace('xxx','xxx')<br>与get_text()或text属性<br>配合使用即可**|
>|**`flow.response.make(status_code: int = 200, content: Union[bytes, str] = b'', headers: Union[mitmproxy.net.http.headers.Headers, Mapping[str, Union[str, bytes]], Iterable[Tuple[bytes, bytes]]] = ()) -> 'Response'`**|**生成新的response的<br>status_code、content、headers等等<br>用于修改或替换response<br>主要用途：捉弄别人**|**直接使用<br>`flow.response.make(status_code=200,content=b'',headers={'xxx':'xxx'})`<br>返回Response对象**|
>|**`flow.response.refresh(self, now=None)`**|**让response响应进行重播<br>调整日期，过期和最后修改的标题<br>调整Cookie的到期时间**|**常用于`refresh(new_time)`<br>这个new_time可以和<br>Python的time模块配合<br>`new_time=time.time()`**|

***
<h1 id="message">Message对象</h1>
<h2 id="message-attribute">Message对象属性</h2>

>**Request和Response同时继承Message对象**
>>|**Message对象属性**|**作用**|**例子**|
>>|:----:|:--------:|:--------:|
>>|**`flow.request/flow.response.headers`**|**获取协议头**|**直接`headers`即可**|
>>|**`flow.request/flow.response.http_version`**|**获取HTTP协议版本**|**直接使用<br>`http_version`**|
>>|**`flow.request/flow.response.content`**|**未压缩的HTTP消息主体<br>(以bytes为单位)**|**直接`content`即可**|
>>|**`flow.request/flow.response.raw_content`**|**可能被压缩过的HTTP消息主体<br>(以bytes为单位)**|**直接使用<br>`raw_content`**|
>>|**`flow.request/flow.response.text`**|**返回text**|**直接使用`text`即可**|
>>|**`flow.request/flow.response.timestamp_start`**|**获取request第一个字节的<br>时间戳**|**直接使用`timestamp_start`**|
>>|**`flow.request/flow.response.timestamp_end`**|**获取request最后一个字节的<br>时间戳**|**直接使用<br>`timestamp_end`**|
>>|**`flow.request/flow.response.trailers`**|**获取request的trailers<br>Trailer规定哪个标头<br>用作分块消息后的元信息**|**直接`trailers`即可**|

***
<h2 id="message-method">Message对象方法</h2>

>**Request和Response同时继承Message对象**
>>|**Message对象方法**|<div style="width:250px">作用</div>|**例子**|
>>|:--------:|:--------:|:----------:|
>>|**`flow.request/flow.response.decode(self, strict=True)`**|**根据当前request的<br>Content-Encoding标头进行解码<br>当Content-Encoding标头不存在<br>并且strict=True时<br>即触发ValueError异常**|**直接`decode()`即可**|
>>|**`flow.request/flow.response.encode(self, e)`**|**对当前request进行编码<br>编码格式有：<br>"gzip", "deflate", "identity", "br"**|**直接使用`encode('gzip')`**|
>>|**`flow.request/flow.response.get_state(self)`**|**获取request的状态**|**直接使用`get_state()`**|
>>|**`flow.request/flow.response.set_state(self, state)`**|**设置request的状态**|**直接使用`set_state(state)`<br>要和`get_state()`<br>配合使用**|
>>|**`flow.request/flow.response.get_content(self, strict: bool = True) -> bytes`**|**使用内容编码标头<br>(例如gzip)解码的<br>request消息正文**|**直接使用<br>`get_content()`<br>返回二进制数据**|
>>|**`flow.request/flow.response.set_content(self, value: Union[bytes, NoneType]) -> None`**|**设置request的<br>二进制数据**|**直接使用`set_content(b'xxx')`<br>可用`get_content()`查看新内容**|
>>|**`flow.request/flow.response.get_text(self, strict: bool = True) -> Union[str, NoneType]`**|**使用内容编码标头<br>(例如gzip)和Content-Encoding标头字符集<br>解码的HTTP消息正文**|**此方法可以自动解码<br>源码有多种方案解码<br>直接使用`get_text()`<br>返回str**|
>>|**`flow.request/flow.response.set_text(self, text: Union[str, NoneType]) -> None`**|**设置request的text**|**直接使用`set_text('xxx')`<br>可用`get_text()`<br>查看新内容**|

***
<h1 id="multi-dict-view">MultiDictView对象</h1>
<h2 id="multi-dict-view-method">MultiDictView对象方法</h2>

>**Request和Response都能使用MultiDictView对象<br>即：`flow.request/flow.response.query`<br>因为Response通常没有query，所以我这里删掉了<br><br>假如你的URL为：https://xxx.com?xxx=xxx**
>>|**MultiDictView对象方法**|<div style="width:250px">作用</div>|**例子**|
>>|:----:|:--------:|:--------:|
>>|**`flow.request.query`**|**获取MultiDictView对象**|**直接返回<br>`MultiDictView[('data','xxx')]`**|
>>|**`flow.request.query.items(self, multi=False)`**|**获取request的url参数所有键和值(元组形式)<br>默认False返回所有键和值(去重)<br>改为True则返回键的所有值**|**直接items()<br>返回可迭代对象<br>用for语句即可读取**|
>>|**`flow.request.query.get(self, key, default=None)`**|**获取增加request的url参数<br>如果不存在这个键默认为None值**|**直接get('xxx')<br>查看内容请使用items()即可**|
>>|**`flow.request.query.get_all(self, key)`**|**获取request的url参数<br>传递一个键名即可获取<br>不存在则返回空列表**|**直接`get_all('xxx')`<br>返回列表**|
>>|**`flow.request.query.add(self, key, value)`**|**插入request的url参数<br>向后插入一个键和值**|**直接`add('xxx','xxx')`<br>查看内容请使用`items()`即可**|
>>|**`flow.request.query.insert(self, index, key, value)`**|**插入request的url参数<br>在指定位置插入给键和值**|**直接`insert(0,'xxx','xxx')`<br>查看内容请使用`items()`即可**|
>>|**`flow.request.query.keys(self, multi=False)`**|**获取request的url参数的键值<br>默认False返回键的一个重复值(键的唯一性)<br>改为True则返回键的一个值**|**直接keys()<br>直接返回generator生成器<br>用for语句即可读取**|
>>|**`flow.request.query.values(self, multi=False)`**|**获取request的url参数的键值<br>默认False返回键的第一个值<br>改为True则返回键的所有值**|**直接values()<br>返回generator生成器<br>用for语句即可读取**|
>>|**`flow.request.query.setdefault(self，key，default = None)`**|**设置默认值操作<br>增加request的url参数<br>如果不存在这个键默认为None值**|**直接`setdefault('xxx')`或者`setdefault('xxx','xxx')`<br>查看内容请使用`items()`即可**|
>>|**`flow.request.query.set_all(self, key, values)`**|**修改request的url某个参数所有值**|**直接`set_all("xxx",["xxx"])`即可<br>**|
>>|**`flow.request.query.clear(self)`**|**删除request的url所有参数**|**直接`clear()`<br>查看内容请使用`items()`即可**|
>>|**`flow.request.query.popitem(self)`**|**弹栈操作<br>删除并返回request的url参数<br>没有参数即引发KeyError异常**|**直接`popitem()`<br>查看内容请使用`items()`即可**|

***
<h1 id="english-document">官方英文文档</h1>

>**需要提前安装好[mitmproxy依赖](#environment)**
>>|**查看方式**|**查看指令**|**说明**|
>>|:----:|:--------:|:--------:|
>>|**生成静态HTML**|**<br>`python -m pydoc -w mitmproxy.net.http`<br><br>`python -m pydoc -w mitmproxy.coretypes.multidict`<br><br>**|**默认生成到当前目录**|
>>|**在线查看文档**|**`python -m pydoc -p 55555`**|**如果55555端口被占用（可修改）<br>启动55555端口成功后<br>访问[mitmproxy.net.http](http://127.0.0.1:55555/mitmproxy.net.http.html)<br>访问[mitmproxy.coretypes.multidict](http://127.0.0.1:55555/mitmproxy.coretypes.multidict.html)**|