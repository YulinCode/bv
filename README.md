# 中二病研究所·弹幕视频网站

## 中二病研究所·弹幕视频网站样例
[样例地址（官方样例）](https://www.imooc.com/ '中二病研究所·弹幕视频网站')

## 声明
该项目为 雨霖 带领“中二病研究所”原创，遵循MIT开源协议\
若发现BUG，请再三检查无误后，联系QQ(3132411278) 或微信（15262028528），请备注BUG信息！

### 为避免重名，现列出部分“雨霖”信息
江苏徐州，2023年初三，男，徐州市第三十三中学

## 项目介绍
中二病研究所·弹幕视频网站是一个基于Go+Vue的前后端分离项目。Web 端使用 Vue + NaiveUI , 后端使用 Golang + Gin + Gorm进行开发。

## 部署方法
请查看文件里的部署txt文件夹

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




