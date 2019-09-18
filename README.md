![Goku API Gateway 悟空网关](https://data.eolinker.com/XvyujZze7c71e4d2ae5f2fd07206f1afaa02289bac346ef.jpg "Goku API Gateway 悟空网关")

Goku API Gateway （中文名：悟空 API 网关）是一个基于 Golang + MySQL + Redis 开发的微服务网关，能够实现高性能 HTTP API 转发、多租户管理、API 访问权限控制等目的，拥有强大的自定义插件系统可以自行扩展，并且提供友好的图形化配置界面，能够快速帮助企业进行 API 服务治理、提高 API 服务的稳定性和安全性。

# 概况

- [为什么要使用Goku](#为什么要使用Goku "为什么要使用Goku")
- [产品特性](#产品特性 "产品特性")
- [快速安装](#快速安装 "快速安装")
- [企业支持](#企业支持 "企业支持")
- [关于我们](#关于我们 "关于我们")
- [授权协议](#授权协议 "授权协议")

# 为什么要使用Goku
Goku API Gateway （悟空 API 网关）是运行在企业系统服务边界上的微服务网关。当您构建网站、App、IOT甚至是开放API交易时，Goku API Gateway 能够帮你将内部系统中重复的组件抽取出来并放置在Goku网关上运行，如进行用户授权、访问控制、流量监控、防火墙、静态数据缓存、数据转换等。

Goku API Gateway 的社区版本（CE）拥有完善的使用指南和二次开发指南，代码使用纯 Go 语言编写，拥有良好的性能和扩展性，并且内置的插件系统能够让企业针对自身业务进行定制开发。

并且 Goku API Gateway 支持与 EOLINKER 旗下的 API Studio 接口管理平台结合，对 API 进行全面的管理、自动化测试、监控和运维。

总而言之，Goku API Gateway 能让业务开发团队更加专注地实现业务。

# 产品特性
- **集群管理**：多个 Goku API Gateway 节点，配置信息自动同步，支持多集群部署。
- **界面管理后台**：通过清晰的UI界面对网关的各项配置进行管理。
- **负载均衡**：对后端服务器进行负载均衡。
- **服务发现**：从 Consul、Eureka 等注册中心发现后端服务器。
- **转发代理**：通过转发请求来隐藏真实后端服务，支持 Rest API、Webservice。
- **多租户管理**：根据不同的访问终端或用户来判断。
- **访问鉴权**：Basic、API Key等。
- **API监控**：请求数据统计。
- **API告警**：支持通过API、邮件方式对异常的服务进行告警。
- **健康检查**：动态发现异常的网关节点以及后端节点，自动切断转发流量并转到其他正常后端服务。
- **异常自动重启**：网关节点异常时会自动尝试重载重启。
- **灵活的转发规则**：支持模糊匹配请求路径，支持改写转发路径等。
- **插件系统**：基于 Go 语言的插件系统，可以快速开发高性能的插件。
- **性能扩展**：网关节点拥有良好的处理性能，支持水平扩展节点数量满足不同的性能需求。
- **日志**：详细的系统日志、请求日志等。
- **Open API**：提供 API 对网关进行操作，便于集成。
- ...

# 快速安装

> 建议控制台与节点分别部署在不同的服务器上，一般一台服务器/虚拟机部署一个网关节点。
> 若要使用多个网关节点，需要将节点文件放置进多台服务器，并且在控制台新建多个节点。
> 此处网关控制台与节点的部署指南，点击查看：[源码编译教程](https://help.eolinker.com/#/tutorial/?groupID=c-350&productID=19 "源码编译")

### 环境准备
* **linux系统，内核版本 2.6.23+**
* **mysql ，版本 5.7+**
* **redis ，版本 2.8+，推荐3.0及以上版本**
* **net-tools**
* **golnag12.x**

### 控制台安装

1.安装:
```
  mkdir -p {install dir}
  mv console-{version}.tar.gz {tmp}/
  cd {tmp}/
  tar -xzf console-{version}.tar.gz
  cd console-{version} && ./install.sh {install dir}
  cd {install dir}

```

2.首次安装进入{install dir}/console/config文件夹，编辑配置文件内容，配置语法参照yaml

**goku.conf** 如下：
```
admin_bind: 绑定节点获取配置的地址，形如IP:Port，填写内网地址或本机地址
listen_port: 管理后台监听端口，可以开放给外网访问
db_host: 数据库Host
db_port: 数据库监听端口
db_name: 数据库名称
db_user: 登录数据库的用户名
db_password: 登录数据库的密码
```

**cluster.yaml** 配置示例如下：
```
cluster:
  -
    name: "GZ"                      # 机房主键，具有唯一性，仅支持英文（不区分大小写）、下划线、数字
    title: "广州机房"                # 机房名称，具有唯一性，用于控制台显示集群名称，支持中文、英文（不区分大小写）、下划线、数字
    note: "从机房"                   # 机房备注，支持中文、英文（不区分大小写）、下划线、数字
    db:
      driver: "mysql"               # 数据库类型，可选项：mysql
      host: "127.0.0.1"             # 数据库主机地址
      port: 3306                    # 数据库端口号
      userName: "root"              # 用户名
      password: "123456"            # 数据库密码
      database: "goku_ce"           # 数据库名称
    redis:
      mode: "cluster"                  # redis模式，可选项：stand/cluster
      addrs: "127.0.0.1:6379,127.0.0.1:6380"    # 多个地址间用英文逗号隔开
      password: "123456"            # redis密码
      dbIndex: 0                    # redis序号，非必填，默认值为0 
      masters: mymaster             # 当mode为sentinel时，该选项必填
  -
    name: "BJ"
    title: "北京机房"
    note: "主机房"
    db:
      driver: "mysql"
      host: "127.0.0.1"
      port: 3306
      userName: "root"
      password: "123456"
      database: "goku_ce"
    redis:
      mode: "stand"
      addrs: "127.0.0.1:6379" # stand、cluster模式下addrs为redis地址，多个地址间用英文逗号隔开
      password: "123456"
      dbIndex: 0
```

3.初次安装创建数据库goku_ce，并运行{install dir}/console/sql文件夹下的goku_ce.sql，执行数据库脚本：
```
mysql -u用户名 -p密码  {install dir}/console/sql/goku_ce.sql
```

4.进入{install dir}/console文件夹，运行run.sh文件以启动控制台

**首次运行**：
```
.run.sh start {config file} 管理员账号 管理员密码
```

示例：
```
.run.sh start config/goku.conf admin 123456
```

**非首次运行**：
```
.run.sh start|restart
```

5.在浏览器输入服务器IP+程序监听端口号，进入管理后台页面

注：程序监听端口号为goku.conf的配置项listen_port的值

![](http://data.eolinker.com/course/nh4Xc8v24059c115375a87de682a0990ba723916923196f)

至此控制台已安装完成，在控制台新建节点后，需要通过命令行启动节点。


### 节点安装

1.安装：

```
  mkdir -p {install dir}
  mv goku-node-{version}.tar.gz {tmp}/
  cd {tmp}/
  tar -xzf goku-node-{version}.tar.gz
  cd goku-node-{version} && ./install.sh {install dir}
  cd {install dir}
```

2.在管理后台新建节点

（1）登录控制台，点击一级导航的 **集群管理**，进入相应的集群：

![](http://data.eolinker.com/course/Wi3hSZzece73986c4714431437a678ffe5ba86c2e21cc54)

（2）为不同集群 **添加节点** ：

![](http://data.eolinker.com/course/rlWf1dI806427cc64c4b73af34dba35a758a4d9f21e86fc)

（3）点击 **新增节点** 按钮，填写节点信息后点击 **确定**：

>（a）新建节点时的节点IP字段填写内网IP或本地IP  
>（b）若配置文件（goku.conf）中的admin_bind字段值IP部分为127.0.0.1或localhost，此处节点IP必须填写127.0.0.1

![](http://data.eolinker.com/course/S2Zjkdi511ae476f039aa53059e26a8b675458fb29841fc)

3.启动节点程序：

```
 {install dir}/run.sh {start|stop|reload|restart|force-reload} [admin url] [port]

```

此处的admin url值与配置文件（goku.conf）中的admin_bind字段值一致。

示例：
```
 {install dir}/run.sh start 127.0.0.1:7005 7702
``` 

4.检查节点是否正常运行：

进入节点管理页面，若节点的状态显示为运行中，则节点正常启动：

![](http://data.eolinker.com/course/WelR3FK9bbf28e791f4f6450bc4b7992efe0f73afec4845)


# 企业支持
Goku API Gateway EE（企业版本）拥有更强大的功能、插件库以及专业的技术支持服务，如您需要了解可以通过以下方式联系我们。
- **中国大陆服务支持电话**：400-616-0330 法定工作日（9:30-18:00）
- **申请企业版免费试用及演示**：[预约试用](https://wj.qq.com/s2/2150032/4b5e "预约试用")
- **市场合作邮箱**：market@eolinker.com
- **购买咨询邮箱**：sales@eolinker.com
- **帮助文档**：[help.eolinker.com](help.eolinker.com "help.eolinker.com")

# 关于我们
EOLINKER 是领先的 API 管理服务供应商，为全球超过3000家企业提供专业的 API 研发管理、API自动化测试、API监控、API网关等服务。是首家为ITSS（中国电子工业标准化技术协会）制定API研发管理行业规范的企业。

官方网站：[https://www.eolinker.com](https://www.eolinker.com "EOLINKER官方网站")
免费下载PC桌面端：[https://www.eolinker.com/pc/](https://www.eolinker.com/pc/ "免费下载PC客户端")

# 授权协议
```
Copyright 2017-2019 Eolinker Inc.

Licensed under the GNU General Public License v3.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at http://www.gnu.org/licenses/gpl-3.0.html

Unless required by applicable law or agreed to in writing, software distributed under the License is distributed on an "AS IS" BASIS,WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and limitations under the License.
```
