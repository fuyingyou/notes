# Linux学习



### 安装gcc相关 

``` sh
yum -y install gcc
yum -y install gcc-c++
```

### 安装yum相关

```sh
sudo yum install -y yum-utils
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

```sh
yum makecache fast		//更新yum软件包索引
```

### 安装docker

```sh
yum -y install docker-ce docker-ce-cli containerd.io
systemctl start docker		//启动docker
ps -ef|grep docker		//查看状态
docker run hello-world		//helloworld
docker version		//查看版本
```

