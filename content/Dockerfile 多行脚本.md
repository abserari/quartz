```
# syntax = docker/dockerfile:1.4
FROM debian 
-RUN apt-get && \ 
- apt-get install -y vim 
 +RUN <<eot bash 
+ apt-get update 
+ apt-get install -y vim 
eot
```