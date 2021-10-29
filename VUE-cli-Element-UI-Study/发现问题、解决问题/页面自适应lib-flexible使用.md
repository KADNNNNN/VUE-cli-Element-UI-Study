# 1. 安装flexible

`npm install lib-flexible --save`

# 2. 引入flexible

在项目入口文件main.js中添加如下代码，引入flexible

`import 'lib-flexible'`

# 3. px 转 rem

使用 webpack 的 px2rem-loader,自动将px转换为rem

# 4. 安装px2rem-loader

`npm install px2rem-loader --save`

# 5. 配置px2rem-loader

在vue-cli生成的文件中,找到以下文件 build/utils.js,如下图添加配置

```html
const cssLoader = {
   loader: 'css-loader',
   options: {
   	 //这一行为加上去的
     minimize: process.env.NODE_ENV === 'production',
     sourceMap: options.sourceMap
   }
 }
 //这段为加上去的
 const px2remLoader = {
   loader: 'px2rem-loader',
   options: {
     remUnit: 192 //根据视觉稿，rem为px的十分之一，移动端使用750/10为75，PC端使用1920/10为192
   }
 }
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/2020070217363313.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MDk3MDk4Nw==,size_16,color_FFFFFF,t_70)

