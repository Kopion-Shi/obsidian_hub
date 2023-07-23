需求：在容器中常见centos7.9+python3.9.5+django3.2+git拉取代码
流程：
 - 下载基础镜像centos7.9
 - 编写dockerfile -> python3.9.5+django 3.2 +git 拉取代码+前台运行django
 - 构建镜像
 - 创建容器+运行
1.  下载基础镜像centos7.9
	```
	docker pull centos:7.9.2009
	```
2. 编写dockerfile
```shell

#Base images 基础镜像
FROM centos:7.9.2009

##MAINTAINER 维护者信息
MAINTAINER sxl Carl3633524461@outlook.com

#RUN 执行以下命令
#GCC编译器
RUN yum install gcc -y
#安装python 依赖
RUN yum -y install zlib-devel bzip2 bzip2-devel openssl-devel openssl-static ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel libffi-devel lzma

## 安装wget
RUN yum install wget -y
#目录
RUN mkdir -p /data/
WORKDIR /data/


##SQLite升级
RUN wget https://www.sqlite.org/2023/sqlite-autoconf-3420000.tar.gz --no-check-certidicate
RUIN tar -zxvf sqlite-autoconf-3420000.tar.gz
WORKDIR /data/sqlite-autoconf-3420000
RUN ./configure
RUN make&&make install
ENV LD_LIBRARY_PATH="/usr/local/lib"

#python3.9环境
RUN wget https://www.python.org/ftp/python/3.9.5/Python-3.9.5.tgz
RUIN tar -zxvf Python-3.9.5.tgz
WORKDIR /data/Python-3.9.5
RUN ./configure
RUN make&&make install
RUN pip3.9 config set global.index-url Https://pypi.douban.com/simple

##git 
RUN yum install git -y

##git拉取代码
WORKDIR /data/
RUN git clone https://gitee.com/wupeiqi/blog.git

##虚拟环境
RUN pip3.9 install virtualenv
RUN virtualenv /envs/dj --python=python3.9
RUN /envs/dj/bin/pip3.9 install django==3.2

##运行项目
WORKDIR /data/blog

CMD ["/envs/dj/bin/python","manage.py","runserver","0.0.0.0:9000"]
```
3. 构建
```shell
docker build -t sxl/dj:1.0  . -f Dockerfile_study
```
4. 运行
```shell
docker run -d -p 80:9000 sxl/dj:1.0
```