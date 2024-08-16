# 系统概述

* Ollama框架支持多种拓展、多环境支持运行，详见于[官方文档](https://github.com/ollama/ollama/tree/main)。
* [Open WebUI社区](https://openwebui.com/#open-webui-community)有大量二次开发的模型，可以满足各种特定的功能需求.

# 安装Ollama

> [!IMPORTANT]
>
> 若需要在windows、macos系统中安装ollama服务，请参考[官方文档](https://github.com/ollama/ollama/tree/main)或[教程](https://blog.csdn.net/qq_40999403/article/details/139320266?spm=1001.2014.3001.5506)



## 环境条件

- 阿里云服务器


- Ubutun18.04（Linux4.15）


- 2核8G，无gpu

- Python


- Anaconda

  > [!TIP]
  >
  > 建议Ubutun版本为16、18、20
  
  

> [!IMPORTANT]
>
> 为保证在后续安装Open WebUI时的兼容性，最好安装python3.11版本`pip install python3.11`



## 环境准备

### 下载并安装Anaconda

```c++
#下载Anaconda，可根据网络条件选择是否使用镜像源或者其他版本
wget --no-check-certificate --no-check-certificatehttps://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-2024.06-1-linux-x86_64.sh
#安装Anaconda，安装过程中的条例和安装位置默认yes
sh Anaconda3-2024.06-1-Linux-x86_64.sh
```

安装条款一直选择yes，会默认安装路径，会默认设置好环境变量

### 创建Ollama的python虚拟环境

* 验证是否能识别conda命令

​	`conda info --envs`

​	命令行头显示base环境，则conda已经加入环境变量，列表中即为已存在Anaconda中的环境，`conda activate 环境名`即可激活该环境

![conda环境显示](https://github.com/SkyLine999999999/ollama/blob/main/imgs/1.png?raw=true)

## 创建conda环境

### 创建名为Ollama的专属运行环境

```python
# 此处为加速下载，使用清华源 
    conda create --name Ollama python=3.11 --channel https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
```

> [!IMPORTANT]
>
> 若已安装好指定版本的python，只需要`conda create --name Ollama python=3.11`其中paddle_env即为环境名，可自行定义

### 激活Ollama环境

`conda activate Ollama`

命令行头显示了Ollama即切换环境成功

![激活环境](https://github.com/SkyLine999999999/ollama/blob/main/imgs/2.png?raw=true)

# Linux环境安装ollama

* 更改下载配置，避免网络的特殊性导致下载缓慢或超时

​	修改hosts文件，对github.com做ip指向

​	`sudo vim /etc/hosts`

​	输入密码后按i增加以下配置

```c++
# github 注意下面的IP地址和域名之间有一个空格
140.82.114.3 github.com
199.232.69.194 github.global.ssl.fastly.net
185.199.108.153 assets-cdn.github.com
185.199.109.153 assets-cdn.github.com
185.199.110.153 assets-cdn.github.com
185.199.111.153 assets-cdn.github.com
```

![ip指向](https://github.com/SkyLine999999999/ollama/blob/main/imgs/3.png?raw=true)

按esc退出输入模式，再输入`:wq`退出并保存编辑

此时ping一下http://github.com即可看到此时的延迟

![ping延迟图](https://github.com/SkyLine999999999/ollama/blob/main/imgs/4.png?raw=true)

* 下载Ollama

  `curl -fsSL https://ollama.com/install.sh | sh`

​	显示如下：表示安装成功

```c++
Downloading ollama...
######################################################################## 100.0%##O#-#                                                                        
Installing ollama to /usr/local/bin...
Creating ollama user...
Adding ollama user to render group...
Adding ollama user to video group...
Adding current user to ollama group...
Creating ollama systemd service...
Enabling and starting ollama service...
Created symlink /etc/systemd/system/default.target.wants/ollama.service → /etc/systemd/system/ollama.service.
NVIDIA GPU installed.
```

* 查看ollama状态

​	`systemctl status ollama`

* 查看ollama的版本信息以及命令

  `ollama -v`
  若显示版本号则安装完成

  ![查看ollama版本](https://github.com/SkyLine999999999/ollama/blob/main/imgs/5.png?raw=true)

  > [!IMPORTANT]
  >
  > ollama的操作命令跟docker操作命令非常相似：
  > ollama serve    # 启动ollama
  > ollama create    # 从模型文件创建模型
  > ollama show        # 显示模型信息
  > ollama run        # 运行模型
  > ollama pull        # 从注册仓库中拉取模型
  > ollama push        # 将模型推送到注册仓库
  > ollama list        # 列出已下载模型
  > ollama cp        # 复制模型
  > ollama rm        # 删除模型
  > ollama help        # 获取有关任何命令的帮助信息  

  

* 更新ollama

  同理执行安装命令`curl -fsSL https://ollama.com/install.sh | sh`

* 卸载ollama

​	停止并禁用服务

​	`systemctl stop ollama`
​	`systemctl disable ollama`

* 删除服务文件和ollama二进制文件

  `rm /etc/systemd/system/ollama.service `
  `rm $(which ollama)`

* 清理Ollama用户和组
  `rm -r /usr/share/ollama`
  `userdel ollama`
  `groupdel ollama`

  > [!IMPORTANT]
  >
  > 此时ollama只能在本机或者服务器访问，如需要外网访问，需要配置端口或者代理

  

# 访问配置

* 配置外网访问

  cd到目录`/etc/systemd/system`下:`vim ollama.service`

​	在[Service]标签下添加如下两行并保存

​	`Environment="OLLAMA_HOST=:11434"`

​	`	Environment="OLLAMA_ORIGINS=*"`

![配置外网访问](https://github.com/SkyLine999999999/ollama/blob/main/imgs/6.png?raw=true)

* 重启服务

  `systemctl daemon-reload`

  `systemctl restart ollama.service`

# 安装Open WebUI

[^说明]: Open WebUI是基于ollama的一个可视化的web界面

> [!IMPORTANT]
>
> 安装方法主要为三种
>
> 1. docker安装，该方法安装过程较为复杂，但后续维护方便
> 2. 使用git安装，环境要求Node.js >= 20.10 和 Bun >= 1.0.21 并且 Python >= 3.11
> 3. 新版的Open WebUI已经添加到了python包中，可直接用指令安装，要求至少python3.11版本

## 使用命令安装

* 安装Open WebUI

​	`pip install open-webui`

​	第一次运行要加变量指定镜像网站

​	`HF_ENDPOINT=https://hf-mirror.com open-webui serve`

* 运行服务

​	`open-webui serve`

​	到端口http://localhost:8080/即可访问

* 修改设置

  注册用户成功后，需要设置外网链接，即为实际url



> [!IMPORTANT]
>
> 若不需要接入其他在线模型的api key，务必在设置中关闭openai接口，只保留ollama接口，修改ollama接口IP或在本机上不用修改。
>
> ![api设置](https://github.com/SkyLine999999999/ollama/blob/main/imgs/10.png?raw=true)

* 下载模型

  回到服务后端，在启动ollama服务之后，通过指令下载大语言模型

  `ollama run 模型名称`

  例如要下载qwen2模型的1.5b版本

  则运行`ollama run qwen2:1.5b`

  具体ollam支持的模型库可在[模型库](https://ollama.com/library)查看

  ![模型库](https://github.com/SkyLine999999999/ollama/blob/main/imgs/7.png?raw=true)
  
  > [!IMPORTANT]
  >
  > 请根据实际性能条件来安装使用模型，Open WebUI可以同时安装多个模型
  >
  > * 至少需要8GB的运行内存来使用7B数据参数的模型
  > * 至少需要16GB的运行内存来使用13B数据参数的模型
  > * 至少需要32GB的运行内存来使用33B数据参数的模型
  
  

* 启动服务

  在服务器后台，先进入Open WebUI的专属运行环境

  `conda activate Ollama`

  运行服务

  `open-webui serve`

  ![服务启动](https://github.com/SkyLine999999999/ollama/blob/main/imgs/8.png?raw=true)

* 查看运行状态

  访问`http://localhost:11434/`，若显示`Ollama is running`，则服务正在运行。

* 使用服务

  通过`http://localhost:8080/`即可进入使用界面，初次登录需要注册账号，身份为管理员，可设置后续登录者身份默认为用户。
  ![主界面](https://github.com/SkyLine999999999/ollama/blob/main/imgs/9.png?raw=true)
