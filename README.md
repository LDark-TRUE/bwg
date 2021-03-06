# bwg：搬瓦工补货自动提醒，写此项目的初衷是想抢GIA CN2乞丐版。
### 前言
前两天搬瓦工偷偷补货GIA CN2乞丐版（限量款），三网直连，简直是为我量身定做的，
但我没有第一时间得到消息，等我到购买页面的时候发现已经卖完了卖完了……所以就有了这个项目。

### 效果
*每当搬瓦工补货，都能收到微信消息。消息内容包括VPS的基本信息和最新的优惠码。*

![推送酱](https://ws1.sinaimg.cn/mw690/a4d9cbc6ly1fvu3f6bqc5j20v91jltd4.jpg)

![推送酱](https://ws1.sinaimg.cn/mw690/a4d9cbc6gy1fvu34xnpepj20v91jldju.jpg)

![推送酱](https://ws1.sinaimg.cn/mw690/a4d9cbc6gy1fvu35oyddnj20v91jl4n5.jpg)

### 需求
- serverChan的SendKey
- 一台VPS
- php版本 => 5.6（推荐使用php7以上版本，性能很不错）

### 食用方法
为了能第一时间在微信收到补货消息，我选用了serverChan的推送服务。
#### 申请SendKey
- 点击访问[serverChan](http://pushbear.ftqq.com/admin/#/)
- 点击右上角的“注册&登入”，接着用微信扫码登录
- 登录成功后，来到了通道管理界面，点击“新增通道”，然后填写通道信息并新增
- 新增完成后回到通道管理界面，点击通道的“设置”按钮，就可以看到“本通道的订阅二维码”，扫码关注（通知时所有关注了此通道二维码的人都会收到通知消息）
- 回到通道管理界面，点击通道的“发送消息”按钮，便能看到SendKey

#### 修改config.php
ok，现在有了SendKey。修改本项目的配置文件config.php。可以看到sendKey项对应着两个通道，`public_notice`用于向公众推送补货提醒，
`report_errors`用于向开发者报告程序错误，这两个通道实际上就是两个sendKey，如果你搭建只为自己个人使用，建议将两个sendKey通道配置为同一个值。
配置多个sendKey通道，是为了将用户和部署者区分开来，这样方便向部署者推送程序错误和补货通知，向公众只推送补货通知。接着，将products对应的pid改为你希望关注的pid，
这个pid是搬瓦工的商品代号，我已经在配置里写了几个，按已有格式来写就好。至于每个商品对应的别名，随便取一个都成。

#### Demo

![推送酱](https://ws1.sinaimg.cn/large/a4d9cbc6ly1fvu38k7enhj204e042gnu.jpg)

（此Demo只监听搬瓦工 CN2 GIA 乞丐版和香港月付9.9刀版本，建议你们自己搭建，按需关注）

#### VPS
*在vps上安装git和lamp环境之类的我就不多赘述了，相信玩域名和vps的人都会，不会的可以去找一键脚本。以下操作使用的是Centos7，其它操作系统命令大同小异。*
#### clone本仓库源码
```bash
$ git clone https://github.com/luolongfei/bwg.git ./
```
#### 安装crontabs以及cronie
```bash
$ yum -y install cronie crontabs
```
#### 验证
##### 验证crond是否安装及启动
```bash
$ yum list cronie && systemctl status crond
```
##### 验证crontab是否安装
```bash
$ yum list crontabs $$ which crontab && crontab -l
```
#### 添加计划任务
##### 打开任务表单，并编辑
```bash
$ crontab -e

# 任务内容如下
# 此任务的含义是在每分钟执行一次/data/www/bwg.feifei.ooo/路径下的index.php文件
# 注意将/data/www/bwg.feifei.ooo/替换为你自己index.php所在路径
* * * * * cd /data/www/bwg.feifei.ooo/; php index.php >/dev/null 2>&1
```
##### 重启crond守护进程
```bash
$ systemctl restart crond
```
##### 查看当前crond状态
```bash
$ systemctl status crond
```
##### 查看当前计划任务列表
```bash
$ crontab -l
```
到这里就配置好了，have fun.
有任何问题欢迎提[issues](https://github.com/luolongfei/bwg/issues)，有帮到你的话给个star如何~
