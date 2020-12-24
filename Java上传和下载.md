不管是上传和下载，我发现都需要设置请求头之类的配置，才能够完成上传下载功能

## 上传

上传的请求头等设置是在表单里面设置的，比如form表单中设置**`enctype="multipart/form-data"`**

服务器里面对文件的处理

```
#前提：获取文件后
#获取文件名
String filename = file.getOriginalFilename();
String path = "F:\\download";

##判断文件是否已经存在，文件目录是否存在，不在就新建
File filefake = new File(path,filename);

if (!filefake.getParentFile().exists()) { 
	filefake.getParentFile().mkdirs();
}

#把文件传输到指定路径
file.transferTo(new File(path+File.separator+filename));
```



## 下载

下载最重要的是设置**{% label info@Content-Disposition %}**响应头和实体头部**{% label info@Content-Disposition %}**

### Content-Disposition

在常规的HTTP应答中，**{% label info@Content-Disposition %}** 响应头指示回复的内容该以何种形式展示，是以**内联**的形式（即网页或者页面的一部分），还是以**附件**的形式下载并保存到本地。

在multipart/form-data类型的应答消息体中， **{% label info@Content-Disposition %}** 消息头可以被用在multipart消息体的子部分中，用来给出其对应字段的相关信息。各个子部分由在[{% label warning@Content-Type%}](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type) 中定义的**分隔符**分隔。用在消息体自身则无实际意义。

Content-Disposition消息头最初是在MIME标准中定义的，HTTP表单及[`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 请求只用到了其所有参数的一个子集。只有`form-data`以及可选的`name`和`filename`三个参数可以应用在HTTP场景中。

#### 1.作为消息主体中的消息头

在HTTP场景中，第一个参数或者是**`inline`**（默认值，表示回复中的消息体会以页面的一部分或者整个页面的形式展示），或者是`attachment`（意味着消息体应该被下载到本地；大多数浏览器会呈现一个“保存为”的对话框，将`filename`的值预填为下载后的文件名，假如它存在的话）。

{% note default  %}

Content-Disposition: inline
Content-Disposition: attachment
Content-Disposition: attachment; filename="filename.jpg"

{% endnote %}

#### 2.作为multipart body中的消息头

{% note default  %}

Content-Disposition: form-data
Content-Disposition: form-data; attachment
Content-Disposition: form-data; attachment; filename="filename.jpg"

{% endnote %}

> 详细内容请查看[{% label @这里%}](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Disposition)

### Content-Type

**{% label info@Content-Type %}** 实体头部用于指示资源的MIME类型 [media type](https://developer.mozilla.org/zh-CN/docs/Glossary/MIME_type) 。

在响应中，{% label info@Content-Type %}标头告诉客户端实际返回的内容的内容类型。浏览器会在某些情况下进行MIME查找，并不一定遵循此标题的值; 为了防止这种行为，可以将标题 [`X-Content-Type-Options`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/X-Content-Type-Options) 设置为 **nosniff**。

在请求中 (如[`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 或 [`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT))，客户端告诉服务器实际发送的数据类型

> 详细内容请查看[{% label @这里%}](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/Content-Type)

<hr>

### 下载文件的设置


```
#获取文件
File file = new File(url);
String filename = file.getName();		

#设置响应头和实体部分
response.setContentType("application/x-msdownload");
response.setHeader("Content-Disposition", "attachment;filename=" + filename);

特殊的传导机制，把文件传输到响应中，类似response.getOutputStream().write()功能;
Files.copy(file.toPath(), response.getOutputStream());
```

### 在线查看文件的设置

感觉跟下载有点想像，就请求头和实体部分不同而已

```
#获取文件和文件名
File file = new File(url);
String filename = file.getName();

#设置响应头和实体部分
response.setHeader("Content-Disposition", "attachment;filename=" + filename);		response.setContentType("application/pdf;charset=UTF-8");

#把文件存储在缓存流中
byte[] b = new byte[fis.available()];
FileInputStream fis = null;
#把缓虫区的字符流读入b中
fis.read(b);
#往响应中写入数据
response.getOutputStream().write(b);
fis.close();
```

