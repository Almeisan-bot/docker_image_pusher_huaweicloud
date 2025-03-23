# Docker Images Pusher

使用Github Action将国外的Docker镜像转存到华为云私有仓库，供国内服务器使用，免费易用<br>
- 支持DockerHub, gcr.io, k8s.io, ghcr.io等任意仓库<br>
- 支持最大40GB的大型镜像<br>
- 使用阿里云的官方线路，速度快<br>

本项目参考了大佬技术爬爬虾的(https://github.com/tech-shrimp/me)项目**<br>

## 使用方式


### 配置华为云
#### 获取长期访问指令
华为云首页：https://www.huaweicloud.com/<br>
登录华为云，首页搜索容器，选择容器镜像服务 SWR<br>
![image](https://github.com/user-attachments/assets/29bf8d98-7d4e-437e-b172-82526202bc20)
<br>

点击控制台进入容器管理页面<br>
![image](https://github.com/user-attachments/assets/c46b2253-0a52-47ed-9d85-4ffadc4a8062)

<br>

在镜像服务控制台总览页面，选择右上角的登陆指令<br>
![image](https://github.com/user-attachments/assets/aff2a25f-1646-4fbe-9bf2-6ad289011cc1)

生成长期登陆指令并复制指令<br>
![image](https://github.com/user-attachments/assets/7f55a99a-a6ce-4d49-94d2-3853b20a0ab5)

例如我的长期登陆指令为：<br>
```
docker login -u cn-north-4@NKXXXXXXXXXXXXXS -p 2xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx6a2 swr.cn-north-4.myhuaweicloud.com
```


需要保存三个值，后面会用到：<br>
华为云用户名：cn-north-4@NKXXXXXXXXXXXXXS<br>
华为云用户密码：2xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx6a2<br>
华为云仓库地址：swr.cn-north-4.myhuaweicloud.com<br>

#### 创建组织

镜像服务控制台页面选择组织管理，然后创建组织，并复制组织名称<br>
![image](https://github.com/user-attachments/assets/4d5ba05f-00c2-4aac-aca2-0fc74da9269c)

现在已经获取到了到了四个值，以下四个变量分别代表：<br>

HW_REGISTRY：华为云仓库地址<br>
HW_ORG_NAME：华为云组织名称<br>
HW_REGISTRY_USER：华为云用户名<br>
HW_REGISTRY_PASSWORD：华为云用户密码<br>

### Fork本项目
Fork本项目<br>
#### 启动Action
进入自己的项目，点击Action，启用Github Action功能<br>
#### 配置环境变量
进入Settings->Secret and variables->Actions->New Repository secret
![](doc/配置环境变量.png)
将上一步的**四个值**<br>
HW_REGISTRY，HW_ORG_NAME，HW_REGISTRY_USER，HW_REGISTRY_PASSWORD
配置成环境变量

### 添加镜像
打开images.txt文件，添加你想要的镜像 
可以加tag，也可以不用(默认latest)<br>
可添加 --platform=xxxxx 的参数指定镜像架构<br>
可使用 k8s.gcr.io/kube-state-metrics/kube-state-metrics 格式指定私库<br>
可使用 #开头作为注释<br>
![](doc/images.png)
文件提交后，自动进入Github Action构建

### 使用镜像
回到华为云，镜像仓库，点击任意镜像，可查看镜像状态。(可以改成公开，拉取镜像免登录)
![](doc/开始使用.png)

在国内服务器pull镜像, 例如：<br>
```
docker pull registry.cn-hangzhou.aliyuncs.com/shrimp-images/alpine
```
registry.cn-hangzhou.aliyuncs.com 即 ALIYUN_REGISTRY(阿里云仓库地址)<br>
shrimp-images 即 ALIYUN_NAME_SPACE(阿里云命名空间)<br>
alpine 即 阿里云中显示的镜像名<br>

### 多架构
需要在images.txt中用 --platform=xxxxx手动指定镜像架构
指定后的架构会以前缀的形式放在镜像名字前面
![](doc/多架构.png)

### 镜像重名
程序自动判断是否存在名称相同, 但是属于不同命名空间的情况。
如果存在，会把命名空间作为前缀加在镜像名称前。
例如:
```
xhofe/alist
xiaoyaliu/alist
```
![](doc/镜像重名.png)

### 定时执行
修改/.github/workflows/docker.yaml文件
添加 schedule即可定时执行(此处cron使用UTC时区)
![](doc/定时执行.png)
