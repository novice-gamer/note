[toc]

# 一、安装

+ **npm安装**

```shell
# 查看版本
npm -v

# 如果版本过低，需要进行升级
sudo npm install -g npm

# 升级或安装cnpm
sudo npm install cnpm -g

# 安装最新稳定版本
sudo npm install vue@next

# 安装cli
sudo npm install -g @vue/cli

# 安装 @vue/cli-int
sudo npm i -g @vue/cli-init
```



# 二、简单应用

## 2.1 创建第一个vue项目

```shell
$ vue init webpack angel_vue

? Project name angel_vue			# 项目名称，注意此处不能用大写
? Project description A Vue.js project		# 项目描述
? Author 刘桂祥 <17611236100@163.com>		# 项目作者，默认计算机用户名
? Vue build standalone
> Runtime + Compiler:recommended for most users # 运行+编译：被推荐给大多数用户
> Runtime-only:about 6KB lighter min+gzip,but templates (or any Vue-specific HTML) are ONLY 
allowed in .vue files-render functions are required elsewhere #译：只运行大约6KB比较轻量的压缩文件，但只允许模板
? Install vue-router? Yes		# 安装vue的路由插件
? Use ESLint to lint your code? Yes		# 是否使用ESLint检测你的代码？[ESLint 是一个语法规则和代码风格的检查工具，可以用来保证写出语法正确、风格统一的代码。建议选择 ‘N’ 因为选择 ‘Y’ 在做调试项目时,控制台会有很多 黄色警告 提示格式不规范,但其实并不影响项目]
? Pick an ESLint preset Standard		# 
? Set up unit tests Yes		# 是否安装单元测试
? Pick a test runner jest
? Setup e2e tests with Nightwatch? Yes	# 是否安装E2E测试框架NightWatch（E2E，也就是End To End，就是所谓的“用户真实场景” [建议N]
? Should we run `npm install` for you after the project has been created? (recommended) npm  # 项目创建后是否要为你运行“npm install”?这里选择包管理工具 [建议yes,use npm]

   vue-cli · Generated "angel_vue".


# Installing project dependencies ...
# ========================
```

进入项目并运行：

```shell
cd angel_vue
npm run dev
```

![](https://raw.githubusercontent.com/novice-gamer/picture/master/img/20210428143430.png)



其他工具：Vite (推荐)

Vite 是一个 web 开发构建工具，由于其原生 ES 模块导入方式，可以实现闪电般的冷服务器启动。

通过在终端中运行以下命令，可以使用 Vite 快速构建 Vue 项目。

全局安装 create-vite-app：

```shell
sudo npm i -g create-vite-app
```

创建项目

```shell
create-vite-app angel_vue

# 输出结果，提示接下来的操作
Done. Now run:

  cd angel_vue
  npm install (or `yarn`)
  npm run dev (or `yarn dev`)
```

启动项目

```shell
cd angel_vue
sudo npm install

```

## 2.2 目录介绍

| 目录/文件    | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| build        | 项目构建(webpack)相关代码                                    |
| config       | 配置目录，包括端口号等。                                     |
| node_modules | npm 加载的项目依赖模块                                       |
| src          | 这里是我们要开发的目录，基本上要做的事情都在这个目录里。里面包含了几个目录及文件：assets: 放置一些图片，如logo等。components: 目录里面放了一个组件文件，可以不用。App.vue: 项目入口文件，我们也可以直接将组件写这里，而不使用 components 目录。main.js: 项目的核心文件。index.css: 样式文件。 |
| static       | 静态资源目录，如图片、字体等。                               |
| public       | 公共资源目录。                                               |
| test         | 初始测试目录，可删除                                         |
| .xxxx文件    | 这些是一些配置文件，包括语法配置，git配置等。                |
| index.html   | 首页入口文件，你可以添加一些 meta 信息或统计代码啥的。       |
| package.json | 项目配置文件。                                               |
| README.md    | 项目的说明文档，markdown 格式                                |































