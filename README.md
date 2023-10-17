# 中二病研究所·弹幕视频网站

## 声明
该项目为 雨霖 带领“中二病研究所”原创，遵循MIT开源协议\
若发现BUG，请再三检查确认后，联系QQ(3132411278) 或微信（15262028528），请备注BUG信息！

### 为避免重名，现列出部分“雨霖”信息
江苏徐州，2023年初三，男，徐州市第三十三中学

## 项目介绍
中二病研究所·弹幕视频网站是一个基于Go+Vue的前后端分离项目。Web 端使用 Vue + NaiveUI , 后端使用 Golang + Gin + Gorm进行开发。

## 部署方法
### 建议使用Ubuntu 20 Server版部署
### 系统要求：
```
配置要求
├──        最低/推荐
│   ├── CPU 2/6核
│   ├── 内存 4/10G
│   ├── 储存 40/200G
│   ├── 带宽 20/50M
```
```
部署文件基于Ubuntu编写，其他系统请自行解决

sudo apt-get update

安装 git
sudo apt-get install git -y

克隆项目
cd /home
sudo git clone https://github.com/YulinCode/bv

安装docker docker-compose
sudo apt-get install -y docker
sudo apt-get install -y docker-compose

安装node
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash  - 
sudo apt-get install -y nodejs

安装pnpm
sudo npm install -g pnpm

安装nginx
sudo apt-get install -y nginx

修改前端配置
cd /home/leaf/web/utils/src
sudo vi global-config.ts

打包前端项目
cd /home/leaf/web
sudo pnpm i

cd /home/leaf/web/packages/web-client
sudo pnpm run build
sudo mv web /usr/share/nginx/html/web


cd /home/leaf/web/packages/manage-client
sudo pnpm run build
sudo mv manage /usr/share/nginx/html/manage

cd /home/leaf/web/packages/mobile-client
sudo pnpm run build
sudo mv mobile /usr/share/nginx/html/mobile


构建后端
cd /home/leaf
pip install --upgrade requests
pip show requests
pip install --upgrade docker
sudo docker-compose build
sudo docker-compose up -d


编辑后端配置
cd data/config/
sudo vi application.yml

打开防火墙9000,8080端口

配置nginx
cd /etc/nginx/conf.d
sudo vi danmu.conf

添加以下内容并保存
server {
    listen       8080;
	server_name  localhost; #有域名可以把localhost替换为域名
	client_max_body_size 1024M;

    location / {
        root /usr/share/nginx/html/web;
        index index.html index.htm;
        try_files $uri $uri/ @web;
    }

    # 解决history路由问题
    location @web {
        rewrite ^.*$ /index.html;
    }

    #后台管理
    location /manage/ {
        root /usr/share/nginx/html;
        index index.html index.htm;
        try_files $uri $uri/ @manage;
    }

    # 解决后台管理history路由问题
    location @manage {
        rewrite ^.*$ /manage/index.html;
    }

    #移动端
    location /mobile/ {
        root /usr/share/nginx/html;
        index index.html index.htm;
        try_files $uri $uri/ @mobile;
    }

    # 解决移动端history路由问题
    location @mobile {
        rewrite ^.*$ /mobile/index.html;
    }

    # 转发后端
    location /api/ {
		proxy_pass http://127.0.0.1:9000;
		proxy_set_header   Host             $host;
     	proxy_set_header   X-Real-IP        $remote_addr;						
        proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
		proxy_http_version 1.1;
    	proxy_set_header Upgrade $http_upgrade;
    	proxy_set_header Connection "upgrade";
	}
}

重启nginx
sudo nginx -s reload
```

### 特点
- 实现了文件存储在服务器本地或阿里云、腾讯云、七牛云对象存储功能
- 实现了视频自动处理分辨率并将视频切片(MPEG-DASH)功能

## 项目结构
```
├── data docker挂载文件
├── docker-compose.yml
├── server 后端
│   ├── api
│   ├── cache 缓存相关
│   ├── common  一些常量
│   ├── config 后端配置文件
│   │   └── application.yml
│   ├── db 数据库相关
│   ├── Dockerfile
│   ├── domain
│   │   ├── dto
│   │   ├── model
│   │   ├── resp 返回内容
│   │   ├── valid 数据校验
│   │   └── vo
│   ├── go.mod
│   ├── go.sum
│   ├── initialize 初始化
│   ├── logger
│   │   └── logger.go
│   ├── logs 存储日志文件
│   ├── main.go
│   ├── middleware 中间件
│   │   ├── auth.go 权限中间件
│   │   └── cors.go 跨域中间件
│   ├── routes 路由
│   ├── service
│   ├── static 静态文件
│   │   ├── casbin 权限相关
│   │   └── jigsaw 滑块拼图相关
│   ├── test 测试文件
│   ├── upload 用户上传文件
│   │   ├── image
│   │   └── video
│   ├── util
│   └── ws websocket相关
└── web 前端
    ├── apis api相关内容
    ├── components 公共组件
    ├── icons 图标
    ├── package.json
    ├── packages
    │   ├── manage-client 后台管理端项目
    │   ├── mobile-client 移动端项目
    │   └── web-client web端项目
    ├── pnpm-lock.yaml
    ├── pnpm-workspace.yaml
    └── utils 

```

## 截图

|                                  Web端                                  |                                                                    |
| :---------------------------------------------------------------------: | :----------------------------------------------------------------: |
|       ![Web端登录](https://leaf.interastral-peace.com/web_login.png)        |     ![Web端首页](https://leaf.interastral-peace.com/web_home.png)      |
|       ![Web端上传](https://leaf.interastral-peace.com/web_upload.png)       |     ![Web端视频](https://leaf.interastral-peace.com/web_video.png)     |
|     ![Web端个人中心](https://leaf.interastral-peace.com/web_space.png)      |    ![Web端消息](https://leaf.interastral-peace.com/web_message.png)    |
|                               后台管理端                                |                                                                    |
|     ![管理端登录](https://leaf.interastral-peace.com/manage_login.png)      | ![管理端视频管理](https://leaf.interastral-peace.com/manage_video.png) |
| ![管理端轮播图管理](https://leaf.interastral-peace.com/manage_carousel.png) |                                                                    |
|                                 移动端                                  |                                                                    |
|     ![移动端登录](https://leaf.interastral-peace.com/mobile_login.jpg)      |   ![移动端视频](https://leaf.interastral-peace.com/mobile_video.jpg)   |
|    ![移动端评论](https://leaf.interastral-peace.com/mobile_comment.jpg)     |                                                                    |




