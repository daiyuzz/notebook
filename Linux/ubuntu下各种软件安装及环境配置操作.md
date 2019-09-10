1. 查看计算机信息
```
uname -a
```
2. 修改计算机名
```shell
sudo vi /etc/hostname
```

3. 安装搜狗输入法

   1）升级fcitx

   ```
   sudo add-apt-repository ppa:fcitx-team/nightly
   sudo apt-get update
   sudo apt-get install fcitx
   sudo apt-get install -f # 安装一下依赖
   ```

   2）安装搜狗输入法

   ```
   dpkg -i sogoupinyin_2.2.0.0102_amd64.deb
   ```

   3）设置

   ```
   settings -> system settings -> Language Support 
   ```

   如果添加输入法时看不到sougou，重新登录Ubuntu再试。

4. 卸载软件

   ```shell
   sudo apt-get remove unity-webapps-common
   ```

5. 从源码安装VIM8，并设置支持Python扩展

   1）安装依赖

   ```
   sudo apt-get install libncurses5-dev libgnome2-dev libgnomeui-dev \
       libgtk2.0-dev libatk1.0-dev libbonoboui2-dev \
       libcairo2-dev libx11-dev libxpm-dev libxt-dev python-dev \
       python3-dev ruby-dev liblua5.1 lua5.1-dev libperl-dev git
   ```

   2）下载VIM8

   ```
   git clone https://github.com/vim/vim.git
   ```

   3）源码安装

   ```
   ./configure --with-features=huge \
               --enable-multibyte \
               --enable-rubyinterp=yes \
               --enable-pythoninterp=yes \
               --with-python-config-dir=/usr/lib/python2.7/config-i386-linux-gnu \
               --enable-python3interp=yes \
               --with-python3-config-dir=/usr/lib/python3.5/config-3.5m-i386-linux-gnu \
               --enable-perlinterp=yes \
               --enable-luainterp=yes \
               --enable-gui=gtk2 \
               --enable-cscope \
               --prefix=/usr/local
   ```

   参数说明：

   ```
   –with-features=huge：支持最大特性 
   –enable-rubyinterp：启用Vim对ruby编写的插件的支持 
   –enable-pythoninterp：启用Vim对python编写的插件的支持 
   –enable-luainterp：启用Vim对lua编写的插件的支持 
   –enable-perlinterp：启用Vim对perl编写的插件的支持 
   –enable-multibyte：多字节支持 可以在Vim中输入中文 
   –enable-cscope：Vim对cscope支持 
   –enable-gui=gtk2：gtk2支持,也可以使用gnome，表示生成gvim
   -with-python-config-dir：指定Python2路径
   –with-python3-config-dir：指定 Python3路径 
   –prefix=/usr/local：指定安装路径
   ```

   查看configure的结果，可能会出现下面的错误：

   ```
   no terminal library found
   checking for tgetent()... configure: error: NOT FOUND!
         You need to install a terminal library; for example ncurses.
         Or specify the name of the library with --with-tlib.
   ```

   解决办法就是执行下面的命令：

   ```
   sudo apt-get install libncurses5-dev libncursesw5-dev
   ```

   configure成功后，执行``sudo make``

   最后执行``sudo make install``

6. 安装pip

   ```
   sudo apt install python-pip  
   sudo apt install python3-pip
   ```

7. 配置VIM

  <略> .vimrc

8. 安装virtualenv

   ```
   pip install virtualenv
   ```

9. 升级pip

   ```
   pip install --upgrade pip
   ```

10. 安装psm

  ​	psm  pip镜像切换工具

  ```
  sudo pip install psm
  ```


12. tar.bz2解压

    ```
    bzip2 -d  package.tar.bz2  # 把package.tar.bz2解压成package.tar
    tar -xvf  package.tar
    ```

13. 安装dia (流程图工具)

    ```
    sudo apt-get install dia
    ```

14. 安装planner（项目计划

    ```
    sudo apt install planner
    ```

15. 安装Krita

    ```
    sudo add-apt-repository ppa:kritalime/ppa
    sudo apt-get update
    sudo apt-get install krita
    参考：https://launchpad.net/~kritalime/+archive/ubuntu/ppa
    ```

16. 安装VNC server

    ```
    sudo dpkg -i VNC-Server-6.2.1-Linux-x64.deb
    ```

    ```
    Installed systemd unit for VNC Server in Service Mode daemon
    Start or stop the service with:
      systemctl (start|stop) vncserver-x11-serviced.service
    Mark or unmark the service to be started at boot time with:
      systemctl (enable|disable) vncserver-x11-serviced.service

    Installed systemd unit for VNC Server in Virtual Mode daemon
    Start or stop the service with:
      systemctl (start|stop) vncserver-virtuald.service
    Mark or unmark the service to be started at boot time with:
      systemctl (enable|disable) vncserver-virtuald.service

    ```
    在ubuntu中，vnc默认的端口号是5999

    在ubuntu中，共享桌面vnc默认的端口号是

17. 安装gimp

    ```
    sudo apt-get install gimp
    ```

18. 安装kolourpaint

    ```
    sudo apt-get install kolourpaint4
    ```

19. 安装mypaint

    ```
    sudo apt install mypaint
    ```

20. 安装typora

    ```
    # optional, but recommended
    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
    # add Typora's repository
    sudo add-apt-repository 'deb http://typora.io linux/'
    sudo apt-get update
    # install typora
    sudo apt-get install typora
    ```

21. 安装稳定版 Sublime 3

    ```
    echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
    sudo apt-get update
    sudo apt-get install sublime-text
    ```

22. 添加网易源

    1）备份

    ```
    sudo cp /etc/apt/sources.list /etc/apt/sources.list_bak
    ```

    2）添加网易源

    ```
    sudo gedit /etc/apt/sources.list
    ```

    在文件中加入如下信息：

    ```
    deb http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
    deb http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ xenial main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ xenial-security main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ xenial-updates main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ xenial-proposed main restricted universe multiverse
    deb-src http://mirrors.163.com/ubuntu/ xenial-backports main restricted universe multiverse
    ```

    更新

    ```
    sudo apt-get update
    ```

23. 安装ffmpeg

    ```
    sudo add-apt-repository ppa:kirillshkrogalev/ffmpeg-next
    sudo apt-get update && sudo apt-get install ffmpeg

    ```

24. 安装 ssh服务器

    ```
    sudo  apt-get install openssh-server
    ```

25. 设置密钥登陆远端Ubuntu

    1）在本地生成密钥对

    ```
    ssh-keygen -t rsa -C "gavinsun@qq.com"
    # 该命令将在~/.ssh 录下 产  对密钥id_rsa和id_rsa.pub
    ```

    2）把public key放到服务器

    ```
    scp ~/.ssh/id_rsa.pub gavin@miguo: ~/.ssh/authorized_keys
    ```

    

    3）把public key的权限设置为600

    ```
    sudo chmod 600 ~/.ssh/authorized_keys
    ```

    4）使用SSH登陆到远端Ubuntu

    ```
    gavinsun:~ gavinsun$ ssh gavin@miguo
    Welcome to Ubuntu 16.04.2 LTS (GNU/Linux 4.4.0-62-generic i686)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

    103 packages can be updated.
    0 updates are security updates.
    ```


    *** System restart required ***
    gavin@ubuntu:~$ 
    
    ​```
    
    ​



27. 安装npm

    ```
    sudo apt install npm
    ```

28. 安装命令行BT下载工具

    ```
    npm install -g t-get
    ```

29. 安装nodejs

    ```
    sudo apt install nodejs-legacy
    ```

30. 安装种子下载工具

    ```
    sudo apt-get install transmission-cli
    ```

31. 安装代理服务器tinyproxy
    ```
    sudo apt-get install tinyproxy
    ```
    启动代理服务器
    ```
    sudo service tinyproxy start
    ```
    查看代理服务器状态
    ```
    systemctl status tinyproxy.service
    ```

    代理服务器的默认端口是8888，你可以通过修改配置文件改变端口

    ```
    sudo vi /etc/tinyproxy.conf
    ```

    ```
    Port 8888 #预设是8888 Port,你可以更改成别的,这里我么修改为8123
    Allow 127.0.0.1 #将127.0.0.1改成你自己的IP，这样只有你可以使用代理
    # 如果注释掉Allow 127.0.0.1，则所有人都可以使用此代理。
    ```

    测试代理是否可用

    ```
    curl -x miguo:8123 www.baidu.com  #假设miguo是你的代理服务器IP
    ```

32. 使用FreeFileSync同步文件

    有界面、开源、多平台、简单易用，适合两台服务器之间同步。


35. 防火墙设置

    ```
    gavin@ubuntu:~$ sudo ufw status
    Status: inactive
    gavin@ubuntu:~$ sudo ufw allow 22/tcp
    Rules updated
    Rules updated (v6)
    gavin@ubuntu:~$ sudo ufw allow 80/tcp
    Rules updated
    Rules updated (v6)
    gavin@ubuntu:~$ sudo ufw allow 7831/tcp
    Rules updated
    Rules updated (v6)
    gavin@ubuntu:~$ sudo ufw allow 8888
    Rules updated
    Rules updated (v6)
    gavin@ubuntu:~$ sudo ufw status
    Status: inactive
    gavin@ubuntu:~$ sudo ufw enable
    ```

    

36. 查看端口

    ```
    netstat -a  # 查看已经连接的服务端口（ESTABLISHED）
    netstat -ap # 查看所有的服务端口（LISTEN，ESTABLISHED）
    netstat -ap|grep 8888 # 查看指定端口
    netstat -tpl
    ```

37. 压缩和解压缩

    > **tar tar.gz**
    >
    > ```shell
    > tar -zcvf cpython.tar.gz cpython/  # 省CPU
    > tar -zxvf cpython.tar.gz  -C /tmp/extract/ 
    > ```

    > **TAR.BZ2** 
    >
    > 这种压缩格式是这几种方式中压缩率最好的。
    >
    > ```shell
    > tar -jcvf cpython.tar.bz2 cpython  # 压缩cpython文件夹
    >
    > tar -jxvf studio.tar.bz2 # 解压到当前目录
    >
    > tar -jxvf cpython.tar.bz2 -C ~/cpython #解压到指定目录
    > ```

    > **TAR** 
    > 好处是只消耗非常少的CPU及时间打包，只是一个打包工具，并不负责压缩。
    >
    > ```shell
    > tar -cvf cpython.tar cpython
    > ```
    >
    > 解包 
    > 到当前目录下
    >
    > ```shell
    > tar -xvf cpython.tar
    > ```
    >
    > 到指定目录下
    >
    > ```shell
    > tar -xvf cpython.tar -C ~/cpython
    > ```

38. **Flask + Gunicorn + Nginx 部署**

    ### 简介

    **WSGI协议**

    Flask致力于如何生成HTML代码,而Web服务器用于处理和响应HTTP请求。Flask和Web服务器之间的通信，需要一套双方都遵守的接口协议。WSGI协议就是用来统一这两者的接口的。

    **WSGI容器——Gunicorn**

    常用的WSGI容器有Gunicorn和uWSGI，性比较而言，Gunicorn配置简单，性能优越。

    **Nginx**

    Nginx是一款轻量级的Web 服务器/反向代理服务器。

    假设我们有一个todos的项目，项目结构如下：

    ```
    gavinsun:python-flask-todos gavinsun$ tree
    .
    ├── README.markdown  # 说明文档
    ├── requirements.txt  # 环境需求
    ├── test.py   # 测试文件
    └── todos  #todos应用包
        ├── __init__.py
        ├── static  # 静态资源
        │   └── style.css  
        ├── templates  # 模板
        │   └── todos.html
        ├── todos.db  # 数据库文件
        ├── todos.py  # todos应用
        ├── todos.sublime-workspace
        └── wsgi.py # 用来启动todos应用
    ```

    wsgi.py代码如下：

    ```python
    from todos import app
    if __name__ == '__main__':
        app.run()
    ```

    不使用Gunicorn，直接启动app的方法如下：

    ```
    (.venv) gavinsun:python-flask-todos gavinsun$ python wsgi.py 
     * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
     * Restarting with stat
     * Debugger is active!
     * Debugger PIN: 281-552-583
    ```

    这样虽然能过运行，但是，性能太差，使用Gunicorn性能会好很多。

    **使用Gunicorn启动app**

    ```
    (.venv) gavinsun:python-flask-todos gavinsun$ pip install gunicorn
    (.venv) gavinsun:python-flask-todos gavinsun$ gunicorn todos.todos:app
    [2018-04-07 10:48:30 +0800] [3458] [INFO] Starting gunicorn 19.7.1
    [2018-04-07 10:48:30 +0800] [3458] [INFO] Listening at: http://127.0.0.1:8000 (3458)
    [2018-04-07 10:48:30 +0800] [3458] [INFO] Using worker: sync
    [2018-04-07 10:48:30 +0800] [3461] [INFO] Booting worker with pid: 3461
    或者
    (.venv) gavinsun:python-flask-todos gavinsun$ cd todos/
    (.venv) gavinsun:todos gavinsun$ gunicorn wsgi:app
    [2018-04-07 10:49:46 +0800] [3465] [INFO] Starting gunicorn 19.7.1
    [2018-04-07 10:49:46 +0800] [3465] [INFO] Listening at: http://127.0.0.1:8000 (3465)
    [2018-04-07 10:49:46 +0800] [3465] [INFO] Using worker: sync
    [2018-04-07 10:49:46 +0800] [3468] [INFO] Booting worker with pid: 3468
    ```

    ## 部署过程

    创建专门用于部署Web应用的用户

    ```
    $ useradd deploy
    $ mkdir /home/deploy
    $ chown deploy:deploy /home/deploy
    $ usermod -a -G sudo deploy
    $ passwd deploy
    $ chsh -s /bin/bash deploy
    ```

    

    准备工作，安装如下工具

    ```
    sudo apt install ufw python-dev python-virtualenv python-pip git nginx supervisor
    ```

    安装虚拟环境（一个项目对应一个虚拟环境）

    ```
    $ mkdir ~/.virtualenvs && cd ~/.virtualenvs
    $ virtualenv flask-todos
    $ source ~/.virtualenvs/flask-todos/bin/activate
    ```

    下载项目代码，安装requirements.txt

    ```
    (flask-todos)$ mkdir ~/sites && cd ~/sites
    (flask-todos)$ git clone https://github.com/alexandersimoes/flask-todos.git (flask-todos)$ cd flask-todos
    (flask-todos)$ pip install -r requirements.txt
    ```

    **安装Nginx**

    ```
    sudo apt install nginx
    ```

    **配置Nginx**

    ```
    (flask-todos)$ sudo rm /etc/nginx/sites-enabled/default
    (flask-todos)$ sudo nano /etc/nginx/sites-available/flask-todos
    ```

    flask-todos配置文件如下：

    ```
    server {
        listen 80;
        server_name 97.64.81.242;

        root /home/deploy/sites/flask-todos;

        access_log /home/deploy/sites/flask-todos/logs/nginx/access.log;
        error_log /home/deploy/sites/flask-todos/logs/nginx/error.log;

        location / {
            proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_redirect off;
            if (!-f $request_filename) {
                proxy_pass http://127.0.0.1:8000;
                break;
            }
        }
        location /config{
            alias /home/gavin/config/;
            autoindex on;
        }

        location /static {
            alias  /home/deploy/sites/flask-todos/todos/static/;
            autoindex on;
        }
    }
    ```

    创建用于保存nginx  log的文件路径

    ```
     (flaskdeploy)$ mkdir -p ~/sites/flaskdeploy/logs/nginx
    ```

    创建符号链接，启用flask-todos配置

    ```
    (flaskdeploy)$ sudo ln -s /etc/nginx/sites-available/flaskdeploy /etc/nginx/sites-enabled/
    (flaskdeploy)$ sudo nginx -t
    (flaskdeploy)$ sudo service nginx restart
    ```

    关于sites-available和sites-enabled的说明：

    ```
    The sites-available folder is for storing all of your vhost configurations, whether or not they're currently enabled.

    The sites-enabled folder contains symlinks to files in the sites-available folder. This allows you to selectively disable vhosts by removing the symlink.

    You should edit files only in sites-available directory.
    Do never edit files inside the sites-enabled directory, otherwise you can have problems if your editor runs out of memory or, for any reason, it receives a SIGHUP or SIGTERM.
    ```

    总之，在sites-available中创建网站的配置文件，在sites-enabled中创建要启用的网站的配置文件的符号链接。千万不要在sites-enabled中直接创建配置文件。

    **使用supervisor管理 gunicorn 命令**

    安装

    ```
    sudo apt install supervisor
    ```

    **把flask-todos启动命令添加到supervisor中进行管理**

    创建配置文件flask-todos

    ```
    sudo vi /etc/supervisor/conf.d/flask-todos.conf
    ```

    在flask-todos.conf中添加如下内容

    ```
    [program:flask-todos]
    command = /home/deploy/.virtualenvs/flask-todos/bin/gunicorn wsgi:app -w 2
    directory = /home/deploy/sites/flask-todos/todos
    user = deploy

    stdout_logfile = /home/deploy/sites/flask-todos/logs/gunicorn/gunicorn_stdout.log 
    stderr_logfile = /home/deploy/sites/flask-todos/logs/gunicorn/gunicorn_stderr.log 
    redirect_stderr = True
    environment = PRODUCTION=1
    ```

    执行下面3条命令，如果没有报错，flask-todos将以后台服务的形式启动。

    ```
    sudo supervisorctl reread
    sudo supervisorctl update
    sudo supervisorctl start flask-todos
    ```

    至此，部署完毕！


40. 进程管理工具supervisor

    安装

    ```
    sudo apt install supervisor
    ```

    重启

    ```
    sudo service supervisor restart
    ```

    Adding a Program

    ```
    [program:flask-todos]
    command = /home/deploy/.virtualenvs/flask-todos/bin/gunicorn wsgi:app -w 2
    directory = /home/deploy/sites/flask-todos/todos
    user = deploy

    stdout_logfile = /home/deploy/sites/flask-todos/logs/gunicorn/gunicorn_stdout.log 
    stderr_logfile = /home/deploy/sites/flask-todos/logs/gunicorn/gunicorn_stderr.log 
    redirect_stderr = True
    environment = PRODUCTION=1
    ```





