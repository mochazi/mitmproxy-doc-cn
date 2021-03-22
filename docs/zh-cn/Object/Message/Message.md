# Request和Response同时继承Message对象
## Message对象属性

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


## **Message对象方法**
>>|**Message对象方法**|**作用**|**例子**|
>>|:--------:|:--------:|:----------:|
>>|**`flow.request/flow.response.decode(self, strict=True)`**|**根据当前request的<br>Content-Encoding标头进行解码<br>当Content-Encoding标头不存在<br>并且strict=True时<br>即触发ValueError异常**|**直接`decode()`即可**|
>>|**`flow.request/flow.response.encode(self, e)`**|**对当前request进行编码<br>编码格式有：<br>"gzip", "deflate", "identity", "br"**|**直接使用`encode('gzip')`**|
>>|**`flow.request/flow.response.get_state(self)`**|**获取request的状态**|**直接使用`get_state()`**|
>>|**`flow.request/flow.response.set_state(self, state)`**|**设置request的状态**|**直接使用`set_state(state)`<br>要和`get_state()`<br>配合使用**|
>>|**`flow.request/flow.response.get_content(self, strict: bool = True) -> bytes`**|**使用内容编码标头<br>(例如gzip)解码的<br>request消息正文**|**直接使用<br>`get_content()`<br>返回二进制数据**|
>>|**`flow.request/flow.response.set_content(self, value: Union[bytes, NoneType]) -> None`**|**设置request的<br>二进制数据**|**直接使用`set_content(b'xxx')`<br>可用`get_content()`查看新内容**|
>>|**`flow.request/flow.response.get_text(self, strict: bool = True) -> Union[str, NoneType]`**|**使用内容编码标头<br>(例如gzip)和Content-Encoding标头字符集<br>解码的HTTP消息正文**|**此方法可以自动解码<br>源码有多种方案解码<br>直接使用`get_text()`<br>返回str**|
>>|**`flow.request/flow.response.set_text(self, text: Union[str, NoneType]) -> None`**|**设置request的text**|**直接使用`set_text('xxx')`<br>可用`get_text()`<br>查看新内容**|