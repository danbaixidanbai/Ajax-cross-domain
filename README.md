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
常用方式为：jsonp：jsonp为js脚本<script>，因为JSONP之所以能够用来解决跨域方案,主要是因为 <script> 脚本拥有跨域能力。<br>
但是后台需要改动需要继承 AbstractJsonpResponseBodyAdvice类 构造方法调用父类来实现后台默认的协议（前后端默认为callback）<br>
	
	
#### jsonp缺点:
1.服务器需要改动<br>
2.只支持get请求，不支持post请求<br>
3.发送的不是XHR请求，最好通过解决URL跨域比较好<br>
		
### 通过被调用方和调用方方式解决：
#### 被调用方方式:
可以通过filter实现，若为简单协议：即方法为：post,get,head,请求header里面无自定义头，且Content-Type为以下几种：<br>
text/plain,multipart/form-data,application/x-www-form-urlencoded<br>
在filter中：<br>



	




