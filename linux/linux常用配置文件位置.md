## 1、nginx启动项和配置文件：

启动项：/usr/local/nginx/sbin的nginx文件

重启：./nginx -s reload



配置文件：/usr/local/nginx/conf下的nginx.conf文件

## 2、nginx的安装包：

/usr/src的里面

## 3、centos启动默认设置配置文件

/boot/grud/grud.conf文件

## 4、tracker和storage的启动项、配置文件

启动项：/usr/bin/fdfs_trackerd 和/usr/bin/fdfs_storaged

重启：/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart

/usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart





配置文件：/etc/fdfs/tracker.conf和/etc/fdfs/storage.conf

## 5、tracker和storage的数据文件（文件服务器存储的文件）的位置

/home/fastdfs

