## Response对象属性

>|**Response对象属性**|**作用**|**例子**|
>|:----:|:--------:|:--------:|
>|**`flow.response.status_code`**|**获取response的状态码**|**直接`status_code`即可**|
>|**`flow.response.cookies`**|**获取response的cookies**|**返回MultiDictView对象<br>直接`flow.response.cookies`即可**|
>|**`flow.response.reason`**|**获取response的reason<br>注意：HTTP/2不包括reason**|**返回MultiDictView对象<br>直接`flow.response.cookies`即可**|

## Response对象方法

>|**Response对象方法**|<div style="width:300px">作用</div>|**例子**|
>|:----:|:--------:|:--------:|
>|**`flow.response.text.replace(self, pattern, repl, flags=0, count=0)`**|**替换response的内容<br>pattern：被替换的内容(正则表达式模式)<br>repl：替换的内容<br>flags：标记数量<br>count：替换次数**|**常用于text.replace('xxx','xxx')<br>与get_text()或text属性<br>配合使用即可**|
>|**`flow.response.make(status_code: int = 200, content: Union[bytes, str] = b'', headers: Union[mitmproxy.net.http.headers.Headers, Mapping[str, Union[str, bytes]], Iterable[Tuple[bytes, bytes]]] = ()) -> 'Response'`**|**生成新的response的<br>status_code、content、headers等等<br>用于修改或替换response<br>主要用途：捉弄别人**|**直接使用<br>`flow.response.make(status_code=200,content=b'',headers={'xxx':'xxx'})`<br>返回Response对象**|
>|**`flow.response.refresh(self, now=None)`**|**让response响应进行重播<br>调整日期，过期和最后修改的标题<br>调整Cookie的到期时间**|**常用于`refresh(new_time)`<br>这个new_time可以和<br>Python的time模块配合<br>`new_time=time.time()`**|