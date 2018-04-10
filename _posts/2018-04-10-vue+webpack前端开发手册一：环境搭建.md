# vue+webpack前端开发手册一：环境搭建

## 1. 运行环境

前端项目是在node环境下开发和测试的，最理想的运行环境是Linux和MacOS，但是目前考虑到我们的开发环境是windows所以选择了使用能在windows10上运行的Linux——WSL，当然，node也有windows的版本，这个是迫不得已的选项，因为项目在windows环境中可能碰到很多莫名的错误。

### 如何安装WSL

首先，你的windows10版本号必须是1709及以上的版本号

定位到：控制面板》程序》启用或关闭windows功能》勾选 适用于Linux的windows子系统

打开windows应用商店，搜索ubuntu（也可以根据自己喜好选择其他Linux发行版），安装

安装完成后就可以在开始菜单中找到ubuntu的图标，可以打开bash终端，也可以通过搜索bash打开

> bash默认是root账户，可以通过修改设置指定其他账户

## 2. 安装node

从官网下载node的Linux安装文件，自行安装，这个网上有教程，不详述

安装完成后可以使用node -v命令查看node版本，使用npm -v查看npm版本

## 3. 安装项目依赖

在WSL中，windows的所有盘符全部都挂载在根路径的mnt目录下，所以如果想定位到d盘下，就是 
```bash
cd /mnt/d
```
这样，就可以定位到windows的任意目录下

目前我们的项目是使用SVN管理的，使用eclipse检出后它的路径在workspace目录下，所以定位到项目路径下的fesrc目录下，这个fesrc就是前端项目的根目录

运行

```bash
npm install
```
安装依赖

如果没有出错的话，一般会显示在多少秒内安装了多少包之类的信息，如果只有WARN信息，可以忽略

> npm是node的包管理器，JavaScript包管理器，使用npm命令有时候可能会长时间没有响应，这是因为国内网络环境的特殊原因，如果实在无法使用，可以考虑使用淘宝的npm镜像，具体使用方法直接百度

### 4. 开发环境预览

依赖安装完毕之后，运行

```bash
npm run dev
```
可以启动webpack-dev-server实时预览项目，这个时候修改的内容可以即时生效，如果要部署到最终的项目中，需要运行

```bash
npm run build
```
打包所有的静态资源

### End



