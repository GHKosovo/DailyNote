## Get请求

`Get`请求:envelope:的参数是`Get`请求的附带信息，不再请求体内，而在请求体外，`Post`请求才是在请求体内

那为什么还用获取`Get`请求参数还需要用到`Request`对象呢:question:

通过查询源码:scroll:，知道原来获取`the query string or posted form data`都需要通过`Request`对象的`getParameter`方法来获取的，而如果时`Post`请求数据，则一般通过`Request`对象的`getInputStream`或`getReader`方法来实现

## 使用HttpUrlConnection来实现Get/Post请求

发现`Get`请求使用`connection`方法来实现发送请求:e-mail:需要开启和关闭，但是`Post`却不用，那到底是什么原因呢:question:，原来`Post`请求有`DoInput/DoOutput`属性，这个属性允许这个连接向服务器自动提交数据和获取数据。所以，一般看到关闭缓存流:ocean:之类的，其实就在自动发送请求啦

## 请求方法类型

请求的方法类型有`get`、`post`、`put`、`delete`等:mailbox_with_mail:，除了在前端页面设置，一般都是在`controller`层设置它的类型

