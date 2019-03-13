# Ajax-cross-domain
ajax跨域问题笔记

## 跨域出现的原因：
请求的url地址,必须与浏览器上的url地址处于同域上,也就是域名,端口,协议相同<br>
若不相同会跨域，因为浏览器会启用同源策略保护，而导致报错<br>
浏览器对于javascript的同源策略的限制,例如a.cn下面的js不能调用b.cn中的js,对象或数据(因为a.cn和b.cn是不同域),所以跨域就出现了<br>
## 解决方式：
①浏览器限制<br>
②通过更换XHR(XmlHttpRequest)请求<br>
③通过被调用方和调用方方式解决<br>
## 以下主要以第二和第三为主
### 通过更换XHR(XmlHttpRequest)请求：
常用方式为：jsonp
	
	




