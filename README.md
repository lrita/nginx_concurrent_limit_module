# nginx_concurrent_limit_module:
This is a third part module for nginx. It make nginx upstream can limit concurrent-level for per backend server.  
It's only support round robin mode for now.  
This is a patch for nginx-1.9.9.  

#config:
Set concurrent in configure file.  
```concurrent=2```&nbsp;&nbsp;means the backend server can receive 2 request in same time.  
```concurrent```&nbsp;&nbsp;means the count of request in the same time, not qps.  
If all backend reach concurrent-limited, nginx will give a 502 error to users.  


upstream backend  
{  
&nbsp;&nbsp;&nbsp;&nbsp;server 127.0.0.1:9090 concurrent=2;  
&nbsp;&nbsp;&nbsp;&nbsp;server 127.0.0.1:9091 concurrent=2;  
}  

#Usage and Complie:
```go
patch -p1 < nginx-1.9.9.patch
./configure
make
make install
```  

#Chinese Describtion:
&nbsp;&nbsp;&nbsp;&nbsp;在实际生产过程中，可能每台backend server的配置不同，导致存在不同的系能瓶颈，一旦请求并发数超过阈值，会造成雪崩效应，使服务器连阈值内的请求量都处理不了。  
&nbsp;&nbsp;&nbsp;&nbsp;如果使用简单的weigth权重控制，并不能解决这种困境。如果请求峰值超过了系统所能承载的并发，仍然会造成雪崩效应。  
&nbsp;&nbsp;&nbsp;&nbsp;为了解决以上的情形，开发了此补丁，当系统瓶颈主要在backend server时，该补丁能很好的抑制backend server陷入雪崩效应的发生。  
&nbsp;&nbsp;&nbsp;&nbsp;可以根据压测结果，配置每一台backend server的并发数```concurrent```。当backend server正在处理的请求超过concurrent值后，upstream就不再会选给该backend server转发新的请求。
