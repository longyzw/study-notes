# Nginx

## 安装

- docker拉取ngnix镜像

  ```sh
  docker pull nginx
  ```

- 挂载主机目录

  ```sh
  docker run --name nginx-yls -d -p 80:80 -v /home:/home nginx
  ```

- 查看本地的容器

  ```sh
  docker ps
  ```

- 测试效果，访问ip如果现实nginx欢迎页则代表创建成功

- 打开并配置文件

  - setup1：进入容器内部

    ```sh
    docker exec -ti nginx /bin/bash
    ```

  - setup2：打开配置文件

    ```
    cd etc/nginx/conf.d/
    vim default.conf
    ```

  - setup3：修改配置文件

    ```sh
    server {
        listen       80;
        server_name  localhost;
    
        location / {
            #root   /usr/share/nginx/html;
         	root    /home/www;  # 修改root根目录，使指向路径为/home/www
            index  index.html index.htm;
        }
    ```

- 重启nginx

  ```sh
  docker container stop nginx-yls
  docker container start nginx-yls
  ```

## 注意事项：

如果在配置文件setup2提示：`vim: command not found`

需要安装 `vim`

```sh
apt-get install vim
```

如果提示：

```sh
Reading package lists… Done
Building dependency tree
Reading state information… Done
E: Unable to locate package vim
```

执行：

```
apt-get update
# 执行完后执行
apt-get install vim
```

如果执行update超时：

```sh
# setup1 复制目前的源
mv  /etc/apt/sources.list /etc/apt/sources.list.bak

# setup2 修改源，由于docker默认没有vim的包 所以无法使用vim指令，所以我们要换个方案
cat <<EOF >/etc/apt/sources.list

deb http://mirrors.ustc.edu.cn/debian stable main contrib non-free
deb http://mirrors.ustc.edu.cn/debian stable-updates main contrib non-free
EOF

# setup3 更新源
执行之前的update 和 install

# setup4 将备份的源恢复回来
mv  /etc/apt/sources.list.bak /etc/apt/sources.list
```

所有操作完成后再访问