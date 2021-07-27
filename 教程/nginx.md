## 配置nginx访问服务器本地文件

最开始是微信公众号上关于网页授权域名的设置文件而引出来的

![image-20210623110217406](F:%5CAA_LLJ%5CGitRepository%5CDailyNote%5C%E6%95%99%E7%A8%8B%5Cimage-20210623110217406.png)

这里要求将某个文件传输到web服务器的目录，将文件放到域名根目录下，但是我部署的项目是在tomcat上的，而且tomcat上根目录上有其他项目了，我地公众号项目是有项目名的，龟龟:turtle::turtle:，这怎么搞啊！

然后通过上网浏览，发现我没必要在tomcat上死磕，因为我的tomcat项目是代理在nginx上的，所以我直接使用nginx来操作就可以了，方便快捷

## 两种方式

1. 字符串拼接

        location = ^~*/images/ {
            root  /data/xxx/data/;
            # root /data/xxx/data/;#指定图片存放路径
            #下面的相关配置我也看不懂，就以上一条配置就行了
            # expires 24h;
            # proxy_store on;
            # proxy_temp_path    /data/xxx/data/;#图片访问路径
            # proxy_redirect     off;
            # proxy_set_header    Host 127.0.0.1;
            # client_max_body_size  10m;
            # client_body_buffer_size 1280k;
            # proxy_connect_timeout  900;
            # proxy_send_timeout   900;
            # proxy_read_timeout   900;
            # proxy_buffer_size    40k;
            # proxy_buffers      40 320k;
            # proxy_busy_buffers_size 640k;
            # proxy_temp_file_write_size 640k;
            # if ( !-e $request_filename)
            #  {
            #     proxy_pass http://127.0.0.1;#默认80端口
            #  }
        
            # autoindex on;
        
        }

注意 要在 /data/xxx/data/ 文件夹里面建立一个 images 文件夹 不然访问不到

可通过 IP/images/图片名  访问了

 

2. 精细化配置

        # 精细化 配置相关静态资源参数，优化访问静态资源文件
        location ~ .*\.(gif|jpg|jpeg|png)$ {
            root /data/fengjing/data/;#指定图片存放路径   
        	
        }
#抄自博主[一粒沙爱上了沙尘暴](https://blog.csdn.net/qq_16720391/article/details/106826281)