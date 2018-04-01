# Introduction

Tars是ctrip开发的应用部署系统，为公司内部不同技术栈的数千款应用提供了随时可用的发布、灰度、回滚、版本管理等功能，月均执行发布数万次。开源版本OpenTars，已经移除了大部分对ctrip系统和组件的依赖（e.g. CMDB、SLB、SaltStack、Build包存储）。在使用相同逻辑层代码的同时，使用Ansible重写了一个基于ssh命令的执行层，开箱即用。当然，开发者也可以自己定制执行层的行为，制作符合自己业务要求的发布系统。

欢迎各位使用、交流，有问题或者建议的话，可以在issue中提出，或者email联系[开发](mailto:mosilent@outlook.com)。


### Feature
* 开箱即用的Tomcat发布系统
* 易用的发布界面，清晰的发布历史和集群内版本状况
* 众多的发布参数可供使用：灰度比例、等待时间、是否自动、点火时长
* 自带元数据模型后台管理，也可通过API对接外部CMDB数据源
* Proven deploy workflow，底层行为通过钩子、重载方法、subclass高度可定制

## Screenshots
![](doc/images/Demo-Screenshots-Deployment-Configure.png)
![](doc/images/Demo-Screenshots-Deployment.png)


# Quick Look & Setup
项目提供了所需基础环境的Dockerfile（包括Tars和Tomcat目标机器），开发者可以通过docker-compose尝试用Tars系统发布一个java demo应用。详情请见[doc/try-in-docker.md](doc/try-in-docker.md)

尽管Tars可以很好的运行在Docker中，当你仍希望old school手动部署时，可以参考如下instructions:

## Dependencies
* Python 2.7
* Redis 2.6+
* (optional) ElasticSearch
* (optional) Sentry

## Install and Run
### Developing
* ```pip install -r requirements/local.txt```
* ```cp settings/local.py.example settings/local.py```
* fill in local.py placebolders of sections:
  * Database
  * Celery
  * constance
  * (optional) REST_CLIENT_SETTINGS
* setup database by:      ```python manage.py migrate```
* create django admin by: ```python manage.py createsuperuser```
* Bring up celery worker: ```python manage.py celery``` worker
* Bring up webserver:     ```python manage.py runserver```
* Superusers can visit localhost:8000/admin to add [meta data](#core-concepts)
* Normal users can visit localhost:8000 to launch a deploy

### Production
* install pip and config local.py, same as in developing
* cd tools --> ```sudo sh deploy.sh```，将配置uWsgi，Nginx，Celeryd


# Core Concepts
* **application**
* **minion, server, group**
* **package**
* **deployment and config**
* **batch**
* **brake**
* **rollback**

# 系统设计与实现
## RollEngine状态机
* pending
* smoke
* bake
* rollout

## Minion Workflow
* batch, auto/manual mode
* pull-download-install-verify-pull

## 通过API创建不同类型的发布

## 高级功能
* Deployment Decision Table
* 合并Group


# Road Map
- [ ] 合并Task与Agency层
- [ ] 剥离ES日志依赖，以及前端读取接口
- [ ] 移除RestClient依赖
- [ ] retry based verify
- [ ] 优化_create_target_canvas，先下载再拉出
- [ ] 新的CMDB后台UI
- [ ] 支持Python3，跟进Celery4.0，Django2.0
- [ ] 使用websocket update前端状态，或者缓存GET请求
- [ ] 使用GraphQL提供API，制作新的前端
