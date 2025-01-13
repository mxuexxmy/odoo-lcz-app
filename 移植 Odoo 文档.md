<font style="color:rgb(48, 48, 48);">移植项目仓库</font>

[https://github.com/mxuexxmy/odoo-lcz-app](https://github.com/mxuexxmy/odoo-lcz-app)



懒猫微服开发者文档

[https://developer.lazycat.cloud](https://developer.lazycat.cloud/)



移植应用来源

应用仓库地址

[https://github.com/odoo/odoo](https://github.com/odoo/odoo)

镜像仓库地址

[https://github.com/minhng92/odoo-18-docker-compose](https://github.com/minhng92/odoo-18-docker-compose)



安装 `lzc-cli` 懒猫微服开发者工具

```shell
npm install -g @lazycatcloud/lcz-cli@latest --registry=https://registry.npmmirror.com
```



安装 `lzc-dtl` 懒猫微服应用转化工具

```shell
npm install -g lzc-dtl --registry=https://registry.npmmirror.com
```



图标来源：[https://github.com/odoo/odoo/blob/18.0/addons/web/static/img/odoo-icon-ios.png](https://github.com/odoo/odoo/blob/18.0/addons/web/static/img/odoo-icon-ios.png)



 `lzc-app` 的图标为 `lzc-icon.png`

更改 `bg-mobile.png` 为 `lzc-icon.png`

```shell
cp odoo-icon-ios.png lzc-icon.png
```



镜像地址

> 不用下载
>

```shell
docker pull postgres:17
dokcer pull odoo:18
```



推送镜像到仓库

> 不用推送，使用 lzc-dtl 的时候，可以进行推送
>

```shell
# 推送镜像
lzc-cli appstore copy-image postgres:17
lzc-cli appstore copy-image odoo:18
```



编写 `docker-compose.yml`

```shell
version: '2'
services:
  postgres-db:
    image: postgres:17
    user: root
    environment:
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo18@2024
      - POSTGRES_DB=postgres
    restart: always
    volumes:
        - ./postgresql:/var/lib/postgresql/data

  odoo18:
    image: odoo:18
    user: root
    depends_on:
      - postgres-db
    ports:
      - "10018:8069"
      - "20018:8072"
    tty: true
    command: --
    environment:
      - HOST=postgres-db
      - USER=odoo
      - PASSWORD=odoo18@2024
    volumes:
      - ./addons:/mnt/extra-addons
      - ./etc:/etc/odoo
    restart: always
```



使用 `lzc-dtl`工具转换

```shell
mxuexxmy@DESKTOP-J2NGGP8:~/project_lazycat/odoo-lcz-app$ lzc-dtl

$$\                                   $$\   $$\     $$\
$$ |                                  $$ |  $$ |    $$ |
$$ |$$$$$$$$\  $$$$$$$\          $$$$$$$ |$$$$$$\   $$ |
$$ |\____$$  |$$  _____|$$$$$$\ $$  __$$ |\_$$  _|  $$ |
$$ |  $$$$ _/ $$ /      \______|$$ /  $$ |  $$ |    $$ |
$$ | $$  _/   $$ |              $$ |  $$ |  $$ |$$\ $$ |
$$ |$$$$$$$$\ \$$$$$$$\         \$$$$$$$ |  \$$$$  |$$ |
\__|\________| \_______|         \_______|   \____/ \__|

欢迎使用懒猫微服应用转换器
这个转换器可以把 docker-compose.yml 方便地转换为 懒猫微服 lpk 应用包。

? 请输入应用名称： Odoo
? 请输入应用包名： cloud.lazycat.app.odoo
? 请输入应用版本： 0.0.1
? 请选择不支持的平台（默认全平台支持）：
? 是否需要限制系统版本？ No
? 请输入应用描述： 一站式全业务链管理平台
? 请输入应用首页：
? 请输入作者： odoo
? 请选择应用功能：
? 请输入子域名： odoo
? 请选择图标文件： lzc-icon.png
? 请选择 docker-compose 文件： docker-compose.yml
? 请选择路由类型： 从docker-compose读取端口
? 是否添加服务 odoo18 的端口映射 10018:8069？ Yes
? 请选择 odoo18:8069 的路由类型： HTTP路由
? 请输入路由路径（如 /api/）： /
? 请输入目标路径（如 / 或 /api/）： /
? 是否添加服务 odoo18 的端口映射 20018:8072？ Yes
? 请选择 odoo18:8072 的路由类型： HTTP路由
? 请输入路由路径（如 /api/）： /
? 请输入目标路径（如 / 或 /api/）： /
? 是否继续添加路由？ No
? [postgres-db] 请选择镜像推送目标： 推送到懒猫微服官方镜像源
[postgres-db] 正在推送镜像到懒猫微服官方镜像源...
命令输出: Waiting ... ( copy postgres:17 to lazycat offical registry)
lazycat-registry: registry.lazycat.cloud/mxuexxmy/library/postgres:7bf15a21a948d5f3
? 如何处理挂载点 /var/lib/postgresql/data？ 挂载空目录
? 请选择 /var/lib/postgresql/data 的挂载位置： 应用内部数据目录 (/lzcapp/var)
? [odoo18] 请选择镜像推送目标： 推送到懒猫微服官方镜像源
[odoo18] 正在推送镜像到懒猫微服官方镜像源...
命令输出: Waiting ... ( copy odoo:18 to lazycat offical registry)
lazycat-registry: registry.lazycat.cloud/mxuexxmy/library/odoo:a498cae86713a721
? 如何处理挂载点 /mnt/extra-addons？ 挂载空目录
? 请选择 /mnt/extra-addons 的挂载位置： 应用内部数据目录 (/lzcapp/var)
? 如何处理挂载点 /etc/odoo？ 挂载空目录
? 请选择 /etc/odoo 的挂载位置： 应用内部数据目录 (/lzcapp/var)

转换完成！已生成应用包：cloud.lazycat.app.odoo.lpk
```



由于使用 `lzc-dtl` 工具转换的配置有问题，所以使用原生的移植方法处理 

参考[移植应用](https://developer.lazycat.cloud/app-example-porting.html)文档

由于之前使用过 `lzc-dtl` 工具，所以已经存在 `icon.png` 图片



编写 `lzc-build.yml` 文档

```shell
pkgout: ./ # 输出的lpk包的位置
icon: ./icon.png   # 图标文件的位置
```



编写 `lzc-manifest.yml` 文档

```shell
lzc-sdk-version: "0.1"
name: Odoo
package: cloud.lazycat.app.odoo
version: 0.0.1
description: 一站式全业务链管理平台
homepage: ""
author: odoo
application:
  subdomain: odoo
  background_task: false
  multi_instance: false
  gpu_accel: false
  kvm_accel: false
  usb_accel: false
  routes:
    - /=http://odoo18.cloud.lazycat.app.odoo.lzcapp:8069/
services:
  postgres-db:
    image: registry.lazycat.cloud/mxuexxmy/library/postgres:7bf15a21a948d5f3
    environment:
      - POSTGRES_USER=odoo
      - POSTGRES_PASSWORD=odoo18@2024
      - POSTGRES_DB=postgres
    binds:
      - /lzcapp/var/postgresql:/var/lib/postgresql/data
  odoo18:
    image: registry.lazycat.cloud/mxuexxmy/library/odoo:a498cae86713a721
    environment:
      - HOST=postgres-db
      - USER=odoo
      - PASSWORD=odoo18@2024
    depends_on:
      - postgres-db
    binds:
      - /lzcapp/var/addons:/mnt/extra-addons
      - /lzcapp/var/etc:/etc/odoo
```



构建应用

```shell
lzc-cli project build -o cloud.lazycat.app.odoo.lpk
```



安装测试测试

```shell
lzc-cli app install cloud.lazycat.app.odoo.lpk

```



上架流程

```shell
lzc-cli appstore publish cloud.lazycat.app.odoo.lpk
```



