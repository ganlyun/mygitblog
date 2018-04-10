# vue+webpack前端开发手册二：生产环境部署

之前已经发过基础的开发文档，帮助大家搭建环境并在node中运行项目进行开发，文章的最后提到了静态资源构建的命令

```
npm run build
```
通过这条命令，就可以将所有需要的文件打包到项目根目录下的 `dist` 文件夹中，在我们的项目里面并没有将 `index.html` 也输出到 `dist` 文件夹中，所以需要把 `dist` 和 `index.html` 都复制到 Tomcat 的 publicpath 文件夹里面进行部署

这时候需要对 index.html 文件进行一个小修改，入口JS的路径由 `/dist/build.js` 修改为 `./dist/build.js`

部署完成后，通过配置项目 `web.xml` 文件将 welcome 指向 `index.html` ，然后就可以直接访问了
