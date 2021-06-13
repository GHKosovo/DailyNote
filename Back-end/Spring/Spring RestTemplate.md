## 概述

借助RestTemplate,Spring应用能够方便地使用REST资源

Spring的 RestTemplate访问使用了**模版方法**的设计模式。其实就是使用接口定义方法，然后实现接口的方法。

## 方法

RestTemplate定义了36个与REST资源交互的方法，其中的大多数都对应于HTTP的方法。
其实，这里面只有11个独立的方法，其中有十个有三种重载形式，而第十一个则重载了六次，这样一共形成了36个方法。

| 方法              | 说明                                                         |
| ----------------- | ------------------------------------------------------------ |
| delete()          | 在特定的URL上对资源执行HTTP DELETE操作                       |
| exchange()        | 在URL上执行特定的HTTP方法，返回包含对象的ResponseEntity，这个对象是从响应体中映射得到的 |
| execute()         | 在URL上执行特定的HTTP方法，返回一个从响应体映射得到的对象    |
| getForEntity()    | 发送一个HTTP GET请求，返回的ResponseEntity包含了响应体所映射成的对象 |
| getForObject()    | 发送一个HTTP GET请求，返回的请求体将映射为一个对象           |
| postForEntity()   | POST 数据到一个URL，返回包含一个对象的ResponseEntity，这个对象是从响应体中映射得到的 |
| postForObject()   | POST 数据到一个URL，返回根据响应体匹配形成的对象             |
| headForHeaders()  | 发送HTTP HEAD请求，返回包含特定资源URL的HTTP头               |
| optionsForAllow() | 发送HTTP OPTIONS请求，返回对特定URL的Allow头信息             |
| postForLocation() | POST 数据到一个URL，返回新创建资源的URL                      |
| put()             | PUT 资源到特定的URL                                          |

## 特点

### 有参数的请求



```java
//有参数的 getForEntity 请求,参数列表
    @RequestMapping("getForEntity/{id}")
    public UserEntity getById2(@PathVariable(name = "id") String id) {
        ResponseEntity<UserEntity> responseEntity = restTemplate.getForEntity("http://localhost/get/{id}", 			UserEntity.class, id);
        UserEntity userEntity = responseEntity.getBody();
        return userEntity;
}
```
