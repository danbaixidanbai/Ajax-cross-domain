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
所谓被调用方方式：基于http协议关于跨域方面的规定：通过http协议响应头部里面增加指定的字段让其支持跨域<br>
而调用方方式则为通过http服务器如:Apache/nginx反向代理，基于隐藏跨域的方式实现
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
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		HttpServletResponse res = (HttpServletResponse) response;
		//*可以换成详细地址
		res.addHeader("Access-Control-Allow-Origin", "*");
		//*可以换成详细get/post方法
		res.addHeader("Access-Control-Allow-Methods", "*");
		chain.doFilter(request, response);
	}
```
若为非简单协议：在工作中常见的有：put,delete方法的ajax请求,发送json格式的ajax请求以及带自定义头的ajax请求<br>
需要在header中加Content-Type
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		HttpServletResponse res = (HttpServletResponse) response;
		//*可以换成详细地址
		res.addHeader("Access-Control-Allow-Origin", "*");
		//*可以换成详细方法
		res.addHeader("Access-Control-Allow-Methods", "*");
		//非简单协议需要加Content-Type
		res.addHeader("Access-Control-Allow-Header", "Content-Type");
		//设置浏览器缓存头时间
		res.addHeader("Access-Control-Max-Age", "3600");
		chain.doFilter(request, response);
	}
```
##### 注意：带cookie的时候，origin必须是全匹配，不能使用*，且需要设置Credentials==true
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		HttpServletResponse res = (HttpServletResponse) response;
		//带cookie的时候，origin必须是全匹配，不能使用*
		res.addHeader("Access-Control-Allow-Origin", "http://localhost:8081");
		//*可以换成详细方法
		res.addHeader("Access-Control-Allow-Methods", "*");
		//非简单协议需要加Content-Type
		res.addHeader("Access-Control-Allow-Header", "Content-Type");
		//设置浏览器缓存头时间
		res.addHeader("Access-Control-Max-Age", "3600");
		chain.doFilter(request, response);
		
		//enable cookie
		res.addHeader("Access-Control-Allow-Credentials", "true");
	}
```
由于带cookie的时候，origin必须是全匹配，不能使用*，所以其他端口无法使用，因此可以利用request.getHeader获取orgin实现通用匹配:
```java
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
			throws IOException, ServletException {

		HttpServletResponse res = (HttpServletResponse) response;
		
		HttpServletRequest req = (HttpServletRequest) request;
		
		String origin = req.getHeader("Origin");
		
		if (!org.springframework.util.StringUtils.isEmpty(origin)) {
			//带cookie的时候，origin必须是全匹配，不能使用*
			res.addHeader("Access-Control-Allow-Origin", origin);			
		}
		
		res.addHeader("Access-Control-Allow-Methods", "*");
		
		String headers = req.getHeader("Access-Control-Request-Headers");
		
		// 支持所有自定义头
		if (!org.springframework.util.StringUtils.isEmpty(headers)) {
			res.addHeader("Access-Control-Allow-Headers", headers);			
		}
		
		res.addHeader("Access-Control-Max-Age", "3600");
		
		// enable cookie
		res.addHeader("Access-Control-Allow-Credentials", "true");
		
		chain.doFilter(request, response);
	}
```
##### 若在spirng框架上解决被调用方：只需要在跨域controller类或者方法上加上@CrossOrigin注解
##### Apache/nginx服务器：

#### 被调用方方式:
Apache服务器：<br>
nginx服务器：反向代理<br>






