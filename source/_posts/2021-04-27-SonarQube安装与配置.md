---
title: SonarQube安装与配置
author: 刘莹鑫
authorLink: https://github.com/lyx383982759
excerpt: 在写代码过程中，难免出现代码写的不规范或存在一些静态的 bug，这时一个良好的代码审查工具就很有必要，而 SonarQube 正好满足这个要求
cover: 2021/04/27/SonarQube安装与配置/cover.jpg
thumbnail: 2021/04/27/SonarQube安装与配置/cover.jpg
categories:
  - - 持续集成&交付&部署
    - 质量控制
tags:
  - 质量控制
  - SonarQube
toc: true
date: 2021-04-27 22:25:29
---
# 1 写在最前面

在写代码过程中，难免出现代码写的不规范或存在一些静态的 bug，这时一个良好的代码审查工具就很有必要，而 SonarQube 正好满足这个要求。

# 2 SonarQube 介绍

[SonarQube](https://docs.sonarqube.org/) 是一款自动代码审查工具，可以检测代码中的 bug 、缺陷和代码气味。它可以与现有的 CI 工具集成，以支持持续代码检查。

# 3 典型开发过程

![img](3-1.png)

- 开发人员在 IDE 中开发代码（可使用 SonarLint 在编写代码时检测和修复问题），然后将其代码提交到代码仓库中。

> [SonarLint](https://www.sonarlint.org/) 是一个 IDE 扩展，可以帮助你在编写代码时检测和修复质量问题。

- 持续集成(我们使用 GitLab CI)工具检出最新代码、构建和运行单元测试，并由集成 SonarQube 的扫描器分析结果。
- 扫描器程序将结果发布到 SonarQube 服务器，该服务器通过SonarQube界面，电子邮件等向开发人员提供反馈。

# 4 SonarQube 安装、配置

> 此章节普通开发人员可忽略。

## 4.1 准备工作

### 4.1.1 系统设置

在命令行窗口执行依次以下命令操作：

- 设置一个进程可能拥有的最大内存映射区域数。

> 不设置这个， sonarqube 启动不起来，

```bash
echo "vm.max_map_count=262144" >> /etc/sysctl.conf
```

- 查看设置的结果：

```bash
sysctl -p
```

![image.png](4.1.1-1.png)

### 4.1.2 Docker 及 Compose 安装

本教程忽略，可参考[https://www.yuque.com/docs/share/3f190b9a-321d-4155-96fd-02b66f8f6d97?#](https://www.yuque.com/docs/share/3f190b9a-321d-4155-96fd-02b66f8f6d97?#) 《Docker安装（资源仓库在线安装）》

## 4.2 创建挂载目录

> 该操作用于建立容器和宿主机之间的目录映射，方便管理和维护。

依次执行以下命令：

- 创建 SonarQube 数据库、日志、配置等存放目录

```bash
mkdir -p /home/sonarqube && cd /home/sonarqube
```

- 创建 SonarQube 映射文件目录

```bash
mkdir -p /home/sonarqube/postgres && \
mkdir -p /home/sonarqube/data && \
mkdir -p /home/sonarqube/extensions && \
mkdir -p /home/sonarqube/logs && \
mkdir -p /home/sonarqube/conf
```

- 创建 postgresql 数据库挂载目录

```bash
mkdir -p /home/sonarqube/postgresql &&\
mkdir -p /home/sonarqube/datasql
```

- 目录设置为 777 权限，避免权限问题

```bash
chmod 777 ./*
```

## 4.3 根据以上创建文件目录信息创建 docker-compose.yml

在 /home/sonarqube/ 目录下创建 docker-compose.yml文件，内容为以下内容：

```yaml
version: '3'
services: 
  postgres: 
    image: postgres
    restart: always
    container_name: sonarqube_postgres
    ports:
      - 5432:5432
    volumes:
      - /home/sonarqube/postgresql/:/var/lib/postgresql
      - /home/sonarqube/datasql/:/var/lib/postgresql/data
    environment:
      TZ: Asia/BeiJing    
      POSTGRES_USER: sonar   
      POSTGRES_PASSWORD: sxxxx
      POSTGRES_DB: sonar
    networks: 
      - sonar-network
  sonar:
    image: sonarqube
    restart: always 
    container_name: sonarqube
    depends_on:
      - postgres
    volumes:
      - /home/sonarqube/extensions:/opt/sonarqube/extensions
      - /home/sonarqube/logs:/opt/sonarqube/logs
      - /home/sonarqube/data:/opt/sonarqube/data
      - /home/sonarqube/conf:/opt/sonarqube/conf
    ports:
      - 9000:9000
    environment:
      SONARQUBE_JDBC_USERNAME: sonar
      SONARQUBE_JDBC_PASSWORD: sxxxx
      SONARQUBE_JDBC_URL: jdbc:postgresql://postgres:5432/sonar
    networks: 
      - sonar-network
networks:
  sonar-network:
    driver: bridge

```

## 4.4 拉取镜像并启动容器服务

在创建 docker-compose.yml 文件的目录下执行以下命令：

```yaml
docker-compose up -d
```

> 该操作首次执行会从互联网下载镜像文件，请确保系统可以联网。

## 4.5 首次访问

访问地址为：服务器IP+端口，本例为 [http://192.168.44.6:9000/](http://192.168.44.6:9000/) 。登录后用户名密码都为 admin admin, 登录后进行修改。
![image.png](4.5-1.png)

## 4.6 系统配置

### 4.6.1 设置中文

按照图示在应用市场安装中文语言包，安装后按照提示重启即可。


![image.png](4.6.1-1.png)

### 4.6.2 邮件配置

- 使用管理员登录系统，按照图示依次点击进入配置页面，配置->配置->通用。

![image.png](4.6.2-1.png)

- 按照以下截图进行邮件配置。

![image.png](4.6.2-2.png)

> ### Email prefix：发邮件时，邮件标题会默认带上该前缀。其他配置项，根据自己邮箱SMTP配置及认证信息进行配置，这里不做详述。

### 4.6.3 GitLab 集成配置

目前不支持，需要 GitLab https　且目前版本为开源社区版本，这里不做具体配置说明。
![image.png](4.6.3-1.png)
![image.png](4.6.3-2.png)

### 4.6.4 LDAP 配置

- 在安装阶段创建的宿主机挂载目录对应的文件 sonarqube/conf/sonar.properties 添加以下内容：

```xml
# LDAP configuration
# General Configuration
sonar.security.realm=LDAP
ldap.url=ldap://192.168.0.xx:389
ldap.bindDn=gitlab@internal.ths.com.cn
ldap.bindPassword=xxx

# User Configuration
ldap.user.baseDn=OU=研发xx,OU=思路xx,DC=domain,DC=solution,DC=com
ldap.user.request=(&(objectClass=user)(sAMAccountName={login}))
ldap.user.realNameAttribute=displayName
ldap.user.emailAttribute=userPrincipalName
```

- 重启 sonarqube

```shell
docker restart sonarqube
```

> 注意这里使用网页中的重启功能是不好使的，需要重启整个容器。

以下为启动后在安装阶段创建的宿主机挂载目录下  /home/sonarqube/logs/web.log 中看到的内容。

```log
2021.04.15 09:42:28 INFO  web[][org.sonar.INFO] Security realm: LDAP
2021.04.15 09:42:28 INFO  web[][o.s.a.l.LdapSettingsManager] User mapping: LdapUserMapping{baseDn=OU=研发中心,OU=思路创新,DC=domain,DC=solution,DC=com, request=(&(objectClass=user)(sAMAccountName={0})), realNameAttribute=displayName, emailAttribute=userPrincipalName}
2021.04.15 09:42:28 INFO  web[][o.s.a.l.LdapSettingsManager] Group mapping: LdapGroupMapping{baseDn=OU=研发中心,OU=思路创新,DC=domain,DC=solution,DC=com, idAttribute=cn, requiredUserAttributes=[uid], request=(&(objectClass=posixGroup)(memberUid={0}))}
2021.04.15 09:42:28 INFO  web[][o.s.a.l.LdapContextFactory] Test LDAP connection on ldap://192.168.0.13:389: OK
```

## 4.7 扫描器安装

### 4.7.1 Windows 环境

#### 4.7.1.1 准备工作

打开对应网站，按照截图下载Windows版本
[https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/)
![image.png](4.7.1.1-1.png)

#### 4.7.1.2 配置

- 将刚才下载的压缩包解压到要存放 扫描器的服务器上。

![image.png](4.7.1.2-1.png)

- 配置环境变量

将解压目录下到bin文件夹的路径配置到环境变量中。

> 本例环境变量配置内容为：D:\Program Files\sonar-scanner-4.6.0.2311-windows\bin;具体如何操作，本例不做详细讲解。

![image.png](4.7.1.2-2.png)

- 验证

命令行输入以下命令：

```shell
sonar-scanner -v
```

![image.png](4.7.1.2-3.png)

> 注意：配合跟 GitLab-Runner 一块使用时，初次配置完 sonar-scanner 环境变量，需要重启下 GitLab-Runner 服务，不然找不到 sonar-scanner 命令。

### 4.7.2 Linux 环境

#### 4.7.2.1 准备工作

打开对应网站，按照截图下载 Linux 版本
[https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/](https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/)
![image.png](4.7.2.1-1.png)

#### 4.7.2.2 配置

- 通过 xshell 等 Linux 运维工具将刚才下载的压缩包上传到服务器上
- 解决压缩包

![image.png](4.7.2.2-1.png)

- 配置环境变量

编辑 /etc/profile 文件，添加解压后的 bin 目录路径进入该文件，本例为 /home/sonar-scanner/bin
![image.png](4.7.2.2-2.png)

- 执行命令使配置环境变量生效

```shell
source /etc/profile
```

- 验证

命令行输入以下命令：

```shell
sonar-scanner -v
```

![image.png](4.7.2.2-3.png)


## 4.8 常见问题

### 4.8.1  执行安装命令后报以下信息：WARNING: IPv4 forwarding is disabled. Networking will not work

![image.png](4.8.1-1.png)
分析：系统设置问题
解决：

- 在宿主机上执行 echo "net.ipv4.ip_forward=1" >>/usr/lib/sysctl.d/00-system.conf
- systemctl restart network && systemctl restart docker

![image.png](4.8.1-2.png)

### 4.8.2 代码扫描结果名称乱码

通过在项目中创建 sonar-project.properties 方式进行项目扫描，当存在项目名称为中午时扫描结果出现乱码
![image.png](4.8.2-1.png)
分析：汉字在 .properties 中可能需要进行 Unicode编码。
解决：
使用命令行扫码，没有乱码问题

```bash
sonar-scanner.bat -D"sonar.projectName=测试123哈哈哈" -D"sonar.projectKey=VUE3-PINXX2" -D"sonar.sources=src" -D"sonar.exclusions=src/js/i18n/**/*" -D"sonar.sourceEncoding=UTF-8" -D"sonar.login=f82121212ccccc"
```

### 4.8.3 Linux 执行 sonar-scanner 报“Could not find 'java' executable in JAVA_HOME or PATH.”

![image.png](4.8.3-1.png)
分析：根据错误是找不到 Java 的可执行程序，通过安装Jdk 发现错误依旧存在，最后发现 sonar-scanner 自带的有 jre ，那就是应该用的自带的 jre，尝试给 jre 目录下的 java 赋予权限解决。
解决：进入 sonar-scanner 所在的目录，转到Java所在目录，执行以下命令

```bash
chmod 755 java
```

# 5 SonarLint 安装

- 打开 VS Code 左侧的 Extensions 模块，搜索SonarLint,点击安装即可。

![image.png](5-1.png)

- 安装后重启 VS Code，保证插件生效。只需打开一个JS、TS、Python、Java、HTML或PHP文件，开始编码，你就会开始看到 SonarLint 报告的问题。问题会在代码中突出显示，也会在“问题”面板中列出。

![image.png](5-2.png)

# 6 GitLab 安装

> 已安装，可忽略。传送：[https://www.yuque.com/heq286/dswu9s/uk2ef0](https://www.yuque.com/heq286/dswu9s/uk2ef0)

# 7 GitLab CI 配置

按照以下说明，进行配置
[http://223.223.179.203:8929/common-resource/gitlab-ci/-/blob/master/README.md](http://223.223.179.203:8929/common-resource/gitlab-ci/-/blob/master/README.md)

# 8 快速上手

本快速上手教程，基于[ 流水线模板](http://223.223.179.203:8929/common-resource/gitlab-ci/-/blob/master/README.md) 进行。可先查看 GitLab CI 配置章节。

## 8.1 获取 Sonar Token 

- 使用公司域账号登录 [http://192.168.0.134:9000/](http://192.168.0.134:9000/)

![image.png](8.1-1.png)

- 进入个人中心-我的账号

![image.png](8.1-2.png)

- 生成 token

输入 token 标识名称，点击生成，即可生成 token。该 token 只会显示一次，需要在当前页面复制保存。当该token不再使用时，可以在该页面点击回收进行删除。
![image.png](8.1-3.png)

> 为了规范，这里的令牌名称按照 GitLab 中的项目名称输入，可以直接在 GitLab 项目列表页面获取。
> 举例：AN20049 深圳市生态环境局大鹏管理局-大鹏新区生态环境动态监测系统（二期） / 前端 / 微应用 / 固定源
> ![image.png](8.1-4.png)

## 8.2 配置 GitLab CI

- 在项目根目录新建 .gitlab-ci.yml 文件(注意是以“.”开头的文件)
- 拷贝以下示例配置信息，修改相关变量信息为自己项目相关部署信息

```bash
include:  - remote: 'http://223.223.179.203:8929/common-resource/gitlab-ci/-/raw/master/templates/web-pipeline.yml'  # 定义变量variables:  # 必填，部署的测试服务器地址,前提是该服务器已配置好ssh免登，格式：服务器用户名@ip，如：weihu@192.168.0.183  SIT_SERVER: "weihu@192.168.0.183(根据实际需求修改)"  # 必填，部署的测试服务器中的物理路径（相对ssh 根路径）,需要先在服务器上对应位置创建目录，如：\Product\nginx-1.18.0\html\arcgis  SIT_DIRECTORY: "\Product\nginx-1.18.0\html\pindd(根据实际需求修改)"   # 测试环境应用的地址，部署后可以访问的HTTP地址  SIT_WEB_URL: "http://192.168.0.183:8104/pindd(根据实际需求修改)"   # 项目类型：project、product,设置对应的项目类型，会自动匹配对应的gitlab-runner，原则上产品类型的项目设置product，项目类型的设置project  PROJECT_TYPE: "project"
```

[查看更多配置](http://223.223.179.203:8929/common-resource/gitlab-ci/-/blob/master/README.md)

- 配置 SONAR_TOKEN

1. 登录 GitLab 打开对应的项目页面-点击设置-CI/CD

![image.png](8.2-1.png)

2. 找到变量那一项，展开

![image.png](8.2-2.png)

3. 点击添加变量

![image.png](8.2-3.png)

4. 添加变量

> SONAR_TOKEN key 名称不能修改，该名称跟模板文件中的 key 名称一致。Value值来自上一步在 Sonar 系统中获取。

![image.png](8.2-4.png)

5. 添加后效果

> 可以在该页面进行编辑及删除等操作

![image.png](8.2-5.png)

## 8.3 修改 Git  strategy 为 git clone

在 GitLab 项目 CICD 中 General pipeline 展开，将 Git  strategy 为 git clone 。
![image.png](8.3-1.png)
该设置会使 Sonar 在分析时，获取文件作者信息。
![image.png](8.3-2.png)

## 8.4 提交代码验证配置

以上配置完毕后，即可提交代码进行首次代码扫描。

- 提交代码后可查看流水线运行情况

![image.png](8.4-1.png)
![image.png](8.4-2.png)

- 待流水线跑完，即可登录 SonarQube 进行扫描结果查看。

> 注意这里要点击所有。

![image.png](8.4-3.png)
![image.png](8.4-4.png)
![image.png](8.4-5.png)

# 9 其他

## 9.1 系统升级

当 SonarQube 使用一段时间后，管理员登录系统后，发现有新的版本可用（如下图），这时可进行更新操作。
![image.png](9.1-1.png)
跨多个非LTS版本的升级可直接进行升级。但如果升级版本跨了一个或多个LTS版本，则必须首先迁移到每个中间LTS，然后再迁移到目标版本。

- 示例1 –从7.1> 8.1，升级过程为7.1> 7.9.6 LTS> 8.1
- 示例2 –从8.2> 8.9 LTS，升级过程为8.2>最新的8.9 LTS。
- 示例3 –从6.7.7 LTS> 8.9 LTS，升级过程为6.7.7 LTS> 7.9.6 LTS>最新的8.9 LTS。

**本升级教程只记录跨多个非LTS版本的升级，如果是跨多个LTS版本的升级，重复多次升级操作即可。**

### 9.1.1 数据库备份

> 此操作是为了防止在升级过程中出现的异常情况或误操作导致数据丢失做备份。升级过程如果正常，此备份没有用。

#### 9.1.1.1 安装数据库管理工具

- 拉取 pgadmin4  数据库管理工具镜像

![image.png](9.1.1.1-1.png)

- 启动 pgadmin4 容器

![image.png](9.1.1.1-2.png)

```bash
docker run -p 5050:80 \    -e "PGADMIN_DEFAULT_EMAIL=liuyx@internal.ths.com.cn" \    -e "PGADMIN_DEFAULT_PASSWORD=s*****n" \    -d dpage/pgadmin4
```

> 这里的邮箱及密码是待会要登录管理系统的密码

- 打开管理页面输入用户名密码（上一步设置的）登录

![image.png](9.1.1.1-3.png)

#### 9.1.1.2 创建服务器连接

![image.png](9.1.1.2-1.png)
![image.png](9.1.1.2-2.png)

> 本示例中的数据库连接等配置，对应 SonarQube 安装章节设置值。

#### 9.1.1.3 执行备份

![image.png](9.1.1.3-1.png)
![image.png](9.1.1.3-2.png)
![image.png](9.1.1.3-3.png)

### 9.1.2 升级

- 修改之前安装 SonarQube 的 docker-compose.yml 文件,添加要升级的版本 TAG ，本示例路径：/home/sonarqube/docker-compose.yml

![image.png](9.1.2-1.png)

- 在 docker-compose.yml 文件所在目录下执行 docker-compose up -d

```bash
docker-compose up -d
```

![image.png](9.1.2-2.png)

- 命令执行完后，访问系统地址，出现以下界面

![image.png](9.1.2-3.png)

- 打开本示例系统设置地址 [http://192.168.0.134:9000/setup](http://192.168.0.134:9000/setup) ，点击升级

![image.png](9.1.2-4.png)
![image.png](9.1.2-5.png)

- 执行以上操作完成后，重新使用admin登录，确认版本是否已升级成功

![image.png](9.1.2-6.png)

- 点击了解这个风险

![image.png](9.1.2-7.png)

- 到系统界面查看版本已经升级到指定版本了

![image.png](9.1.2-8.png)

## 9.2 系统迁移

> 为防止迁移过程中数据丢失，建议迁移前进行数据库备份。

- 将容器和宿主机之间的映射目录，整体迁移到新的机器。例如本例中老机器的目录为/home/sonarqube/下的所有文件：

![image.png](9.2-1.png)

- 拷贝到新的机器上目录设置为 777 权限，避免权限问题

```bash
chmod 777 ./*
```

- 根据迁移文件目录信息更新 docker-compose.yml
- 在 docker-compose.yml 文件的目录下执行以下命令：

```yaml
docker-compose up -d
```

> 该操作首次执行会从互联网下载镜像文件，请确保系统可以联网。

- 执行完成后，打开登录页验证，服务器IP+端口（docker-compose.yml 中配置端口）
