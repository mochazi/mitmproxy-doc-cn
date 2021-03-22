# 查看文档

>**需要提前安装好[mitmproxy依赖](zh-cn/quick-start/quick-start.md)**
>>|**查看方式**|**查看指令**|**说明**|
>>|:----:|:--------:|:--------:|
>>|**生成静态HTML**|**<br>`python -m pydoc -w mitmproxy.net.http`<br><br>`python -m pydoc -w mitmproxy.coretypes.multidict`<br><br>**|**默认生成到当前目录**|
>>|**在线查看文档**|**`python -m pydoc -p 55555`**|**如果55555端口被占用（可修改）<br>启动55555端口成功后<br>访问[mitmproxy.net.http](http://127.0.0.1:55555/mitmproxy.net.http.html)<br>访问[mitmproxy.coretypes.multidict](http://127.0.0.1:55555/mitmproxy.coretypes.multidict.html)**|