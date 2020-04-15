

### ISSO搭建评论系统

###### 依赖软件版本：

- Python 2.6, 2.7 or 3.6+ (+ devel headers)
- Virtualenv
- SQLite 3.3.8 or later
- a working C compiler
- Node.js, NPM and Bower

Ubuntu默认会安装Python2和Python3. 如何选择哪个版本的Python，可执行如下选择：

```
// 设置Python2为默认
sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 100
// 设置Python3为默认
sudo update-alternatives --install /usr/bin/python python /usr/bin/python3 150
// 切换Python版本
sudo update-alternatives --config python
```

安装Pip，[pip](https://pypi.python.org/pypi/pip) (Python Package Index) 是 Python 软件包管理系统

```
// apt包管理器更新软件包列表
sudo apt update
// Python3安装pip3
sudo apt install python3-pip

pip3 --version
root@iZwz94u1jmuzilyxlpjtjuZ:~# pip --version
pip 8.1.1 from /usr/lib/python3/dist-packages (python 3.5)

pip install xx
pip uninstall xx
// 列出已安装软件包
pip list
```

安装必要的Python库

```
sudo apt-get install python-dev build-essential -y
```

安装sqlite3

```
sudo apt-get install sqlite3
新建一个数据库目录
mkdir -p /var/lib/isso
```

安装nodejs/npm

https://www.cnblogs.com/liuqi/p/6483317.html

安装Bower

```
npm install -g bower
```

安装完bower后，执行bower发现找不到命令，需要将路径加到Path中

https://www.cnblogs.com/perallina/p/5671292.html

```
# Set Path
PATH=$PATH:/root/software/nodejs/bin
export PATH
```

##### 从Pip安装Isso

```bash
pip install isso
```

 isso 已经被完整安装在 `/usr/local/bin/isso` 了

##### 从源码安装Isso

克隆仓库

```
git clone https://github.com/posativ/isso.git
cd isso/
```

创建一个虚拟环境

```
virtualenv .
source ./bin/activate
```

安装 Isso 及其依赖

```
python setup.py develop

```

执行安装报错，

错误1: 从提示看是cffi没有安装且版本cffi>=1.3.0

(cffi是连接Python与c的桥梁，可实现在Python中调用c文件。cffi为c语言的外部接口，在Python中使用该接口可以实现在Python中使用外部c文件的数据结构及函数。)

```
(isso) root@iZwz94u1jmuzilyxlpjtjuZ:~/software/isso# python setup.py develop
WARNING: The repository located at mirrors.cloud.aliyuncs.com is not a trusted or secure host and is being ignored. If this repository is available via HTTPS we recommend you use HTTPS instead, otherwise you may silence this warning and allow it anyway with '--trusted-host mirrors.cloud.aliyuncs.com'.
ERROR: Could not find a version that satisfies the requirement cffi>=1.3.0 (from versions: none)
ERROR: No matching distribution found for cffi>=1.3.0

```

A: 首先根据提示更新Pip版本后，执行

```
sudo pip install cffi
```

错误2: 提示Flask安没有安装，执行`sudo pip install Flask`

```
Extracting Flask_Caching-1.8.0-py3.5.egg to /root/software/isso/lib/python3.5/site-packages
Flask-Caching 1.8.0 is already the active version in easy-install.pth

Installed /root/software/isso/lib/python3.5/site-packages/Flask_Caching-1.8.0-py3.5.egg
error: The 'Flask' distribution was not found and is required by flask-caching
```

错误3: 安装Flask时报错

```
ERROR: Package 'Jinja2' requires a different Python: 3.5.2 not in '>=3.6'
```



Ubuntu16.04默认安装了Python2.7和Python3.5，需要升级本地Python>3.5， 选择不卸载已有Python版本新安装新版本

sudo add-apt-repository ppa:jonathonf/python-3.6

sudo apt-get update

sudo apt-get install python3.6



调整Python3的优先级，使得3.6优先级较高

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.5 100

sudo update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.6 150

更新Python为Python3.6后，安装Pip报错

https://blog.csdn.net/qq_27366789/article/details/80559074

安装python3-pip后， pip --version

```
root@iZwz94u1jmuzilyxlpjtjuZ:~# pip --version
Traceback (most recent call last):
  File "/usr/local/bin/pip", line 7, in <module>
    from pip._internal.cli.main import main
ModuleNotFoundError: No module named 'pip._internal'
```

```
wget https://bootstrap.pypa.io/get-pip.py  --no-check-certificate
sudo python get-pip.py
```

```
python-software-properties

于是

apt-get install python-software-properties

除此之外还要安装

software-properties-common

于是

apt-get install software-properties-common

然后就能用add-apt-repository了

root@iZwz94u1jmuzilyxlpjtjuZ:~# sudo add-apt-repository ppa:jonathonf/python-3.8
 Backport of Python 3.8 from Sid.

---

Donate to Python: https://www.python.org/psf/donations/
Donate to Debian: https://www.debian.org/donations
Donate to me: https://ko-fi.com/jonathonf
 More info: https://launchpad.net/~jonathonf/+archive/ubuntu/python-3.8
Press [ENTER] to continue or ctrl-c to cancel adding it

```



安装 JavaScript 模块

```
make init
```

