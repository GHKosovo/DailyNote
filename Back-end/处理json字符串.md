# Json数据

## 处理json字符串

常用的是使用fastjson这个工具来操作

> Json对象，内部是用键值对来存储数据的

通过它，可以把json字符串转化为json对象JsonObject，然后通过JsonObject来获取其内部的JsonObject对象和JsonArray数组对象，也可以通过

```java
//转化json字符串为JsonObject对象
JSONObject jsonObject = JSON.parseObject(jsonfork);
//通过对象的String字符串key值获取对象的值
String orderId = (String) jsonObject.getString("orderId");
//获取Json数组对象
JSONArray order = jsonObject.getJSONArray("order");
```

### JsonObject对象

JsonObject对象可以通过get()方法来获取某对象key对应的值，也可以通过其他类型的getxxx(String key)方法来获取不同类型的值，比如getString()、getInteger()、getFloat()、getDate()等方法；当然也可以自己设置类，返回对应的对象getObject(xxx,Student.Class)

> 参数是String类型

```java
String orderId = (String) jsonObject.getString("orderId");

Phone phone = objects.getObject("order", Phone.class);
```



### JsonArray对象

JsonArray对象其实跟JsonObject对象差不多，只不过，获取内部数据，是通过数组下标，而不是通过key来获取；同样，它也有getxxx(int index)方法来获取不同类型的值，比如getString()、getInteger()、getFloat()、getDate()等方法；

> 只不过参数是下标

```java
Phone phone = objects.getObject(i, Phone.class);
```



## #前言

前台页面通常都会通过`Ajax`来获取后台的数据，某些接口传输数据也都是以`Json`格式来传输，故处理`Json`格式数据边得很重要鸭:duck:

## 处理Json格式数据

在微信返回的消息中，有些是以json数据包的格式呈现的:point_down:

```json
{
  "access_token":"ACCESS_TOKEN",
  "expires_in":7200,
  "refresh_token":"REFRESH_TOKEN",
  "openid":"OPENID",
  "scope":"SCOPE" 
}
```

那如何取出这里面的信息呢。那就需要用到<span class="ljspan ljspan-reverse ljspan-red">[fastjson](https://github.com/alibaba/fastjson)</span>啦!:mushroom:

一般从网络:earth_asia:上获取/传输的`json数据包`，都是<span class="ljspan ljspan-red">json字符串</span>，如果需要取出来，需要把它转化为<span class="ljspan ljspan-red">json对象</span>。

```java
//json字符串转json对象
String result = '{"name":"2323","sex":"afasdf","age":"6262"}';
JSONObject jsonObject =JSON.parseObject(result);
//这样就可以从json对象jsonobject中获取到你所需要的信息了	  
String name = jsonObject.getString("name");
```

另一方面，如果要传输`json数据包`​​，一般也是先编写好<span class="ljspan ljspan-red">json对象</span>后，转化为<span class="ljspan ljspan-red">json字符串</span>后传输​。:cactus:

```
{
     "button":[
     {	
          "type":"view",
          "name":"我的报告",
          "url":"/path/to/Report"
      },
      {	
          "type":"click",
          "name":"我的社区",
          "key":"V1001_COMMUNITY"
      },
      {	
          "type":"click",
          "name":"个人中心",
          "key":"V1001_TODAY_MUSIC"
       }]
 }
```

:point_down:下面代码这是对上面:point_up:的实现哦。

```java
//封装json对象
JSONObject jsonObject = new JSONObject();
JSONArray jsonArray = new JSONArray();
JSONObject jsonObject1 = new JSONObject();
jsonObject1.put("type", "view");
jsonObject1.put("name", "我的报告");
jsonObject1.put("url", "/path/to/Report");
JSONObject jsonObject2 = new JSONObject();
jsonObject2.put("type", "click");
jsonObject2.put("name", "我的社区");
jsonObject2.put("key", "V1001_COMMUNITY");
JSONObject jsonObject3 = new JSONObject();
jsonObject3.put("type", "click");
jsonObject3.put("name", "个人中心");
jsonObject3.put("key", "V1001_TODAY_MUSIC");
jsonArray.add(jsonObject1);
jsonArray.add(jsonObject2);
jsonArray.add(jsonObject3);
jsonObject.put("button",jsonArray);
//转化为json字符串
String jsonString = jsonObject.toJSONString();
```

> 使用eclipse提示功能，可以看到许多`jsonObject`和`JSON`的方法！

<hr>


## 自动转化json数据

后台服务可以自动转化某类对象为json格式到前台页面，前端页面可直接取用，你说神不神奇:frog:,它一般结合`@ResponseBody`一起使用

它就是<span class="ljspan ljspan-yellow">[jackson databind](https://github.com/FasterXML/jackson-databind)</span>，貌似上文提到的<span class="ljspan ljspan-red">fastjson</span>也可以实现(不过，还需要其他配置)，但是<span class="ljspan ljspan-yellow">jackson databind</span>只需要引入依赖包就行了:slightly_smiling_face:

```xml
<!--引入依赖包-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.9.8</version>
</dependency> 
```



## Springboot自动包装返回值为Json字符串

现如今，Springboot在Controller层可以使用@ResponseBody注解，则Controller返回数据能够在页面上显示，而且是以json格式。

