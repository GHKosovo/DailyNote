`@RequestBody`用来接收http post请求的body，前端传入序列化好的json数据，后端可以解析为json对象（Content-Type需要指定为 application/json）。

`@RequestParam`用来接收请求url?后面的参数，或者Content-Type为multipart/form-data、application/x-www-form-urlencoded时的http body数据。

