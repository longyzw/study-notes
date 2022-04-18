# Docker

## 安装docker

### 安装最新版本

```sh
sudo yum install docker-ce docker-ce-cli containerd.io
```

### 查看docker版本，确认安装成功

```sh
docker --version
```

### 启动Docker服务并设为开机启动

```sh
systemctl enable --now docker
```

### 确认docker服务运行正常，显示active (running)说明服务正常运行

```sh
systemctl status docker
```

## 配置国内镜像加速

### 阿里云镜像加速

在对应位置创建daemon.json

```sh
tee /etc/docker/daemon.json <<-'EOF'

{
  "registry-mirrors": ["https://uyah70su.mirror.aliyuncs.com"]
}

EOF

```

### 配置完成后重启docker服务

```sh
systemctl daemon-reload && systemctl restart docker
```

## 常用命令

- 查看现有镜像

  ```sh
  docker iamges
  ```

- 查看已启动镜像

  ```sh
  docker ps
  ```

- 查看防火墙

  ```sh
  firewall-cmd --state
  ```

- 关闭防火墙

  ```sh
  systemctl stop firewalld.service
  ```