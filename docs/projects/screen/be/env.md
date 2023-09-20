# Screen-BE 开发环境配置

本文介绍用于测试部署，**请勿在生产环境按此步骤部署！**

## 预备环境

1. python3.6+
2. pip
3. git

## 部署步骤

### 修改 pip 镜像源

为了节约时间，我们来配置一下 pip 镜像源，提高 install 速度。

在`C:\Users\<Windows User Name>\`下创建`pip`文件夹。  
在`C:\Users\<Windows User Name>\pip\`中新建`pip.ini`

输入以下内容：

```ini
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple/
[install]
trusted-host=pypi.tuna.tsinghua.edu.cn
```

或者直接输入

```shell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### 安装 virtualenv

```
pip install virtualenv
```

### 虚拟环境

在合适的文件夹下：

```
virtualenv venv
```

激活：

=== "Windows"

    ```
    .\venv\Scripts\activate.bat
    ```

=== "Linux"

    ```
    source ./venv/Scripts/activate
    ```

如果激活成功，会出现(venv)的字样。

### 下载源代码

```
git clone https://github.com/SMS-COSMO/Screen-BE
cd Screen-BE
```

### 安装 Screen-BE

安装依赖：

```
pip install -r requirements.txt
```

初始化数据库：

```
python manage.py migrate
```

创建超级用户：

```
python manage.py createsuperuser
```

会提示输入用户名，密码（两次，不会回显）

### 全部命令

```
pip install virtualenv
virtualenv --python=python3.10 venv
.\venv\Scripts\activate.bat

git clone https://github.com/SMS-COSMO/Screen-BE
cd Screen-BE

pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

## 运行服务器

```
python manage.py runserver
```

若要在局域网内开启服务，请输入

```
python manage.py runserver 0.0.0.0:<端口>
```

显示类似于：

```
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).
September 20, 2023 - 17:16:46
Django version 3.1b1, using settings 'Screen-BE.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CTRL-BREAK.

```

## 管理

在 http://127.0.0.1:8000/ 可以访问 Django 的 admin 页面。

可能需要输入用户名、密码，使用你在`python manage.py createsuperuser`时输入的用户信息。
