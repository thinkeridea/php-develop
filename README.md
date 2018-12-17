# PHP-develop

`Docker PHP` 开发环境

`PHP` 镜像构建在 [PHP for Dockerfile](./PHP/README.md)

## 使用

需要设置如下环境变量：

| 环境变量名称 | 描述 |
| --- | --- |
| `WEB_PATH` | 网站名录， 就是网站程序根路径 |
| `MYSQL_DATA` | `MySql` 数据库数据目录，本地存储 |
| `MYSQL_ROOT_PASSWORD` | 初次运行需要设置，作为 `MySql` `root` 密码 | 
| `WEB_USER` | `php-fpm` 运行账户，一般为主机账户，有权限对 `${WEB_PATH}` 读写，常配置为用户 `id`，用名称可能在容器内找不到 |
| `WEB_GROUP` | `php-fpm` 运行账户组，一般为主机账户组，有权限对 `${WEB_PATH}` 读写，常配置为用户组 `id`，用名称可能在容器内找不到 |

- 启动服务
    
```shell
$ docker-compose up -d
```

- 重启服务

```shell
$ docker-compose restart
```

- 注销服务

```shell
$ docker-compose down
```

- 为保证 `MySql` 密码安全，不要配置环境变量 `MYSQL_ROOT_PASSWORD`， 而通过运行时设置，例如执行如下命令

> 在首个命令前面加空格，以防止被加入到历史记录中，为了确保安全关闭当前shell窗口， 清空本地历史。
> 重启是为了保证MySql容器中没有这个环境变量，避免容器泄露密码

```shell 
$  MYSQL_ROOT_PASSWORD=123456 docker-compose up -d
$ docker-compose down
$ docker-compose up -d
```

## 注意事项

`php-fpm` 默认与运行用户为`www`， 手动修改 `php-conf.d/user.ini` 的用户和用户组，或者运行下面的 `shell`

```shell 
$ echo -e "[www]\nuser = ${WEB_USER}\ngroup = ${WEB_GROUP}" > php-conf.d/user.ini
```

`Nginx` 运行用户可以在 `docker-compose.yml` `nginx` 服务 `volumes` 配置项下面增加 `./nginx/nginx.conf:/etc/nginx/nginx.conf` 使用自己的配置

配置模版可以通过运行 `docker cp nginx:/etc/nginx/nginx.conf ./nginx/nginx.conf` 修改其中的用户及组信息即可

## 网站跟目录

> 网站目录在镜像中必须保持一致

当前默认配置 `Nginx` 与 `PHP` 镜像中的网站根目录均为 `/var/www`，在配置 `Nginx` 网站时根目录应该是 `/var/www/{project_path}` 

## 关于 `PHP` 连接 `MySql` 错误问题

错误： `SQLSTATE[HY000] [2054] The server requested authentication method unknown to the client`

发生这种错误，是由于 `MySql` 8 默认使用了新的密码验证插件：`caching_sha2_password`，而之前的 `PHP` 版本中所带的 `MySqlnd` 无法支持这种验证。

一种办法是升级 `PHP` 支持 `MySql` 8 的新验证插件。

`PHP 7.2.8` 和 `PHP 7.1.20` 已经可以支持 `caching_sha2_password`，直接连接 `MySql` 8。

`PHP 7.1.8` 和 `PHP 5.6.26` 还无法支持 `caching_sha2_password`。

具体解决办法请参考: [mysql.cnf](mysql/conf.d/mysql.cnf) 中的 `default-authentication-plugin=MySql_native_password` 配置。

## `PHP` 如何连接 `MySql` 和 `Redis`

在 `PHP` 程序中使用如下服务 `host` ：

| 服务名称 | 服务 `host` |
| --- | --- |
| `mysql` | `mysql` |
| `redis` | `redis` |

## `Redis` 持久化问题

修改 `docker-compose.yml` 中 `Redis` 服务配置，如下：

```yaml
redis:
   container_name: redis
   image: redis:alpine
   privileged: true
   restart: always
   command: redis-server /etc/redis.conf
   volumes:
     - ./redis.conf:/etc/redis.conf
   ports:
     - "6379:6379"
```

其中 `./redis.conf` 为自己创建的 `Redis` 配置文件。
