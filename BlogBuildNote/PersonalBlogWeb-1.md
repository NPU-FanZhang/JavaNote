# Personal Blog 搭建指南

[TOC]



# 1、环境搭建

node verison：`12.16.2`

npm  verison：`6.14.4`

vue/cli : `4.5.13`





## 1、脚手架生成Vue3项目

​	在这之前，配置Node为国内镜像：

```shell
npm config set registry https://registry.npm.taobao.org
```

1. 安装vue-cli

   ```shell
   npm install -g @vue/cli
   ```

   安装成功后:

   ```shell
   vue -V
   @vue/cli 4.5.13
   ```

2. 创建Vue项目

   命令行启动UI创建Vue3项目。

   ```shell
   vue ui
   ```

   ![image-20210513215054049](PersonalBlogWeb-1.assets/image-20210513215054049.png)

   其中路由是否选择HTML5 History Mode，可以选也可以不选，在Router/index.js中可以进行设置。

3. 创建好的项目，使用VScode打开，输入`npm run serve`运行。

   ![image-20210513220043045](PersonalBlogWeb-1.assets/image-20210513220043045.png)

4. 前端Vue空项目搭建完成，项目目录如下。

   ```shell
     |__ .browserslistrc
     |__ .git
     |__ .gitignore
     |__ babel.config.js
     |__ node_modules
     |__ package-lock.json
     |__ package.json
     |__ public
       |__ favicon.ico
       |__ index.html
     |__ README.md
     |__ src
       |__ App.vue
       |__ assets
         |__ logo.png
       |__ components
         |__ HelloWorld.vue
       |__ main.js
       |__ router
         |__ index.js
       |__ store
         |__ index.js
       |__ views
         |__ About.vue
         |__ Home.vue
   
   ```

   

