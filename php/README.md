# PHP for Dcoker

> PHP运行账户和组都是 `www`, 需要设置程序 `www` 账户可读，需要PHP写数据的目录设置为 `www` 账号可写

### 开发环境构建的PHP docker镜像，以下是版本列表：

- [PHP 5.6.26 Dockerfile](./5.6.26/Dockerfile)
- [PHP 7.1.8 Dockerfile](./7.1.8/Dockerfile)

### 使用镜像

```shell
# PHP 5.6.26
$ docker run --rm -ti thinkeridea/php:5.6.26 php -m

# PHP 7.1.8
$ docker run --rm -ti thinkeridea/php:7.1.8 php -m
```

### PHP扩展列表

```shell
$ php -m
[PHP Modules]
bcmath
bz2
Core
ctype
curl
date
dom
ereg
exif
fileinfo
filter
ftp
gd
gettext
hash
iconv
json
libxml
mbstring
mcrypt
mhash
mysqlnd
openssl
pcre
PDO
pdo_mysql
pdo_sqlite
Phar
posix
readline
redis
Reflection
session
shmop
SimpleXML
soap
sockets
SPL
sqlite3
standard
sysvsem
tokenizer
xml
xmlreader
xmlrpc
xmlwriter
xsl
zip
zlib

[Zend Modules]

```