## Request对象属性

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

## Request对象方法

>**假如你的URL为：https://xxx.com**
>>|**Request对象方法**|<div style="width:300px">作用</div>|**例子**|
>>|:-------:|:----------:|:-----------:|
>>|**`flow.request.text.replace(self, pattern, repl, flags=0, count=0)`**|**替换request的内容<br>pattern：被替换的内容(正则表达式模式)<br>repl：替换的内容<br>flags：标记数量<br>count：替换次数**|**常用于`text.replace('xxx','xxx')`<br>与get_text()或text属性配合使用即可**|
>>|**`flow.request.make(method: str, url: str, content: Union[bytes, str] = '', headers: Union[mitmproxy.net.http.headers.Headers, Dict[Union[str, bytes], Union[str, bytes]], Iterable[Tuple[bytes, bytes]]] = ()) -> 'Request'`**|**生成新的request的<br>method、url、content、headers等等<br>用于修改或替换request<br>主要用途：捉弄别人**|**返回Request对象<br>直接使用<br>`flow.request.make(method='GET',url='https://xxx.com',headers={'xxx':'xxx'})`**|
>>|**`flow.request.anticache(self)`**|**删除request可能产生的缓存<br>删除了协议头的ETags和If-Modified-Since**|**直接`anticache()`即可**|
>>|**`flow.request.anticomp(self)`**|**删除request可能压缩数据资源的标头**|**直接`anticomp()`即可**|
>>|**`flow.request.constrain_encoding(self)`**|**限制request的Accept-Encoding值<br>方便我们之后解码**|**直接`constrain_encoding()`即可**|