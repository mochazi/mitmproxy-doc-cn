# Request和Response同时继承MultiDictView对象
## MultiDictView方法

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