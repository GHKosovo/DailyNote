## 关于session

session在服务器怎么存储的用户的数据，让用户可以在下次登录的时候，不用重新输入账号密码，就可以直接登录访问了呢？

## session的原理

首先用户登录后，验证账号密码无误后，后台服务器会生成一个jsessionid，这个就是对应这个用户登录的session的，然后后台会响应这个jsessionid给前台，强调一下，**这个jsessionid是存储在cookie里面的**，以cookie的形式返回给前台，当前台再次请求后台时，会把存储再cookie中的jsessionid一并传递到后台，后台再次接收前台请求的时候，从cookie里面获取到这个jsessionid，然后比对后台服务器是否有这个session对应的jsessionid，如果有，就直接可以访问

**当然，有时候，前台也可能禁用cookie**，那这时候，该怎么办嗯？

## 禁用cookie后，如何使用session

不用保存SessionID到cookie中，而是动态地把当前用户的SessionID添加到程序的各超链接或转发地址中,那么就可以确保用户的唯一。response.encodeRedirectURL(url)是一个进行URL重写的方法， 使用这个方法的作用是为了在原来的url后面追加上Jsessionid 。 目的是保证即使在客户端浏览器禁止了cookie的情况下，服务器端仍然能够对其进行事务跟踪。

> 简单得讲，就是response.encodeRedirectURL(url)这个方法可以在链接后面追加jsessionid来让前台接收到