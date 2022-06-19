### 反向代理路径的各种情况

```
    # 情形A
    # 访问 http://www.test.com/testa/aaaa
    # 后端的request_uri为: /testa/aaaa
    location ^~ /testa/ {
        proxy_pass http://127.0.0.1:8801;
    }
    
    # 情形B
    # 访问 http://www.test.com/testb/bbbb
    # 后端的request_uri为: /bbbb
    location ^~ /testb/ {
        proxy_pass http://127.0.0.1:8801/;
    }
 
    # 情形C
    # 下面这段location是正确的
    location ~ /testc {
        proxy_pass http://127.0.0.1:8801;
    }
 
    # 情形D
    # 下面这段location是错误的
    #
    # nginx -t 时，会报如下错误: 
    #
    # nginx: [emerg] "proxy_pass" cannot have URI part in location given by regular 
    # expression, or inside named location, or inside "if" statement, or inside 
    # "limit_except" block in /opt/app/nginx/conf/vhost/test.conf:17
    # 
    # 当location为正则表达式时，proxy_pass 不能包含URI部分。本例中包含了"/"
    location ~ /testd {
        proxy_pass http://127.0.0.1:8801/;   # 记住，location为正则表达式时，不能这样写！！！
    }
 
    # 情形E
    # 访问 http://www.test.com/ccc/bbbb
    # 后端的request_uri为: /aaa/ccc/bbbb
    location /ccc/ {
        proxy_pass http://127.0.0.1:8801/aaa$request_uri;
    }
 
    # 情形F
    # 访问 http://www.test.com/namea/ddd
    # 后端的request_uri为: /yongfu?namea=ddd
    location /namea/ {
        rewrite    /namea/([^/]+) /yongfu?namea=$1 break;
        proxy_pass http://127.0.0.1:8801;
    }
 
    # 情形G
    # 访问 http://www.test.com/nameb/eee
    # 后端的request_uri为: /yongfu?nameb=eee
    location /nameb/ {
        rewrite    /nameb/([^/]+) /yongfu?nameb=$1 break;
        proxy_pass http://127.0.0.1:8801/;
    }

```


