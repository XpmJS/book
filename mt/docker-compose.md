# 使用 Docker Compose 安装团队猫

## 第一步: 安装 Docker

安装 Docker

```bash
sudo -s
curl -sSL https://get.daocloud.io/docker | sh
```

安装加速器

```bash
sudo -s
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://5382404c.m.daocloud.io
```

安装 Docker Compose

```bash
sudo -s
curl -L https://github.com/docker/compose/releases/download/1.13.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
sudo  chmod +x  /usr/local/bin/docker-compose
```

## 第二步: 编写启动配置文件

在用户目录下创建配置文件。

```bash
mkdir ~/docker
vi ~/docker/tuanduimao.yml
```

根据项目情况，修改配置并保存。

```yml
version: '2'
services:
  webserver:
    restart: always
    image: tuanduimao/tuanduimao:1.33
    container_name: tuanduimao
    extra_hosts:
      - some.domain.com:172.17.0.1
    environment:
      - HOST=some.domain.com
      - REDIS=off
      - MYSQL=off
    ports:
      - "80:80"
      - "443:443"
      - "3306:3306"
      - "6379:6379"
    volumes:
      - /host/logs:/logs
      - /host/config:/config
      - /host/apps:/apps
      - /host/data:/data
```

## 第三步: 使用配置文件管理容器

创建启动容器

```bash
docker-compose -f ~/docker/tuanduimao.yml up -d
```

其他操作: 关闭容器

```bash
docker-compose -f ~/docker/tuanduimao.yml down
```

其他操作: 重启容器

```bash
docker-compose -f ~/docker/tuanduimao.yml restart
```

## 第四步: 使用Git检出应用

### 1. 设置 Git 仓库私钥

**不得使用工程师私钥，必须使用 Guest或pub 账号私钥**  
**如没有私钥可在开发管理平台申请**

创建私钥文件

```bash
sudo -s
mkdir -p /root/.ssh
vi /root/.ssh/id_rsa
```

粘贴私钥字符串 **\(以下为无效私钥，请更换为指定的私钥）**

```text
-----BEGIN RSA PRIVATE KEY-----
MIIEowIBAAKCAQEA4aW9kleNf2wjoLoLGmOsKgXyjz3qzswII4MHI4ANNFgVSngt
mhQxO/o6R7NiqXPe78NLknGlvBEBmA4h2vBWjjCnLDLQXpEoxdo5BHQH6kAl7asi
4C8K/JI55aymSvAftfwr1y32adsffax9b+PNNo05bXkeEYXxtqGUw7QzdNXCAPTQ
P2oFwS95BJqwxjfA7xjSc6ZQAtkFBUJJDu1CmoqsoFWTEQVPEVVgUpGFf6N8N2M3
YT7i8rf9Nck3LruvPfo0Jnni2xRN819Xcl2fletBtmYTj4tFGY0/Dz7xJd0/eqgt
ATybiU13e3DpYZ8VLcTAVvPyN7/sPcmuHkdlOwIDAQABAoIBACV13oLtBhChY0jL
mgxHf816L0qYfOLX/IHovsal+4s1FFPIn8l0kLfkUsiUf0yib+BeC63EMD+Ikzsr
HXO7cqMocJhl1zHb52jxUYXrvWSmQaWzQ5b0OF615+a5QuIt+xW7R4vxBYr6hMbX
i/uHVgo4Z9BEyzkdg4NOT+Qthl1ez2a4w9bZV9705gRPVllSMKMX7bijctYzhzKb
vH0DpBei9zkA75PIvDMqmalPPki1FQ8SjyV1eQG+qAicYxJPoENFa3KhqZMM3r2X
vODLsUUbgk6FBN2EOJC+3A4u0PhWP7UlKoxYVufeeV96EwSSshZzM23oNMPw+HZI
jh+QtAECgYEA/q5wo322124358IsiiAxgRb5NGU5fWIU1IreG4vPGst483L9C6+A
OqhbylRZgMGr3CFUZfBn8stqfRIm4hVk1XIZws784QkqOM5jWassgYbeU8fbDTsJ
Z68ZJfRx1xwe0TAezGqCNKS78URtLegKFoVQP8MTI8/ePenDEDa+CucCgYEA4tDR
etMUvLkO9dfDW+dUUiq+2GVcvbHvKxAoVxgydXkRX6wGQZ9xII21hPW6GULIPhsm
M4LNL5tzq6OHtGBtwUH0bQLZtUkamuv8HxVLzFiJvkKzOjkwBxZTa58QIibwT+fJ
Ym+B2tdD8S5GTxVOcCMP3aaddgIZe7XiXzgg/I0CgYEAqUT7bDhPbaOr/+qIe81l
2ayROTfF/AXCXnllod1MazytSPE2KhwdF99qEpH5YtBWD1q/o3kjPYXhYvs7iKw7
dnn9kTLNdCwJOfRCqAhS7kvbXMfKWYLRf24rQsSzHQt9l/9pmOd5Xs/WckbOYeKF
Qe6dJaPcBsNTrMa/dPlNWiUCgYAtpUKTCknBFSkKlqptI2fXxVx05ik8z8NHElBb
/rWg6IVzkIYNzM2SdJJUOLOEA+mSfho5AZjTfOBRaW6VAVb1LpXHHmy7zAN7rAQo
KTwA2syVqoyxKfMdagPNw8wWY2m3WvkvQyuJ5Ap7Tgm+PpZzgMrGWx8oGjuQpvDw
orYYvQKBgDTebEY3y4fQ4XHSM2x1l73SM5cljkT+ol3UUZpelPnJT+uKHzdTLKVI
+OpMYa2vyOOc0Kw5JZoxnbJATQ4otoCSSRIcBywX6a7edYSOeLeWCT5obon/WQuL
xRMvz0PYX0MEAIgjjccrt0iZzNvUxmgy1vGkXIGEOmPu5OmjcZwm
-----END RSA PRIVATE KEY-----
```

### 2. 检出代码

**应用配置信息中 org 和 name 的信息需要与应用目录对应**

例如:  应用配置信息 `package.json`

```json
...
"org":"mina"
"name":"pages"
...
```

则应用必须部署到 `<apps home dir>/<org>/<name>` 文件夹下。

检出代码示例:

```bash
sudo -s
mkdir -p /host/apps/mina
cd /host/apps/mina 
git clone ssh://git@dev.xpmjs.com:2222/source/xxx.git pages
```

## 第五步: 安装团队猫

访问 `http://some.domain.com` 根据提示完成安装

\(全文完\) [返回运维手册](index.md)

