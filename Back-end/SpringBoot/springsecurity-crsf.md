## crsf防护

spring security自身携带有对crsf的防护；如果结合thymeleaf来启用crsf，那么会更加便捷；

## 限制

因为crsf防护本身只针对幂等性的请求方法，比如get请求方法，而对于post等请求方法，则需要使用某些方法来验证通过crsf防护才能访问后台，因为他们是非幂等性；

## 导入关键包

除了导入thymeleaf和spring security的包，它们的相关包thymeleaf-extras-springsecurity5也需要引入，才能够在前端页面使用thymeleaf结合spring security的相关标签；

这样，在发送post请求的时候就，只要action属性使用thymeleaf来表示，就会**自动**在表格中添加隐藏的“_crsf”标签。

![image-20210326102041161](F:\AA_LLJ\GitRepository\DailyNote\Back-end\SpringBoot\springsecurity-crsf.assets\image-20210326102041161.png)

## 异步请求

当然，spring security不支持异步请求；

如果需要使用异步请求，则需要在页面头部中，先获取crsf的相关信息，之后再发送异步请求时，设置到请求头中，就可以了了

```html
<!--访问该页面时,在此处生成CSRF令牌.-->
	<meta name="_csrf" th:content="${_csrf.token}">
	<meta name="_csrf_header" th:content="${_csrf.headerName}">
```

```javascript
// 发送AJAX请求之前,将CSRF令牌设置到请求的消息头中.
    var token = $("meta[name='_csrf']").attr("content");
    var header = $("meta[name='_csrf_header']").attr("content");
    $(document).ajaxSend(function(e, xhr, options){
        xhr.setRequestHeader(header, token);
    });
```

