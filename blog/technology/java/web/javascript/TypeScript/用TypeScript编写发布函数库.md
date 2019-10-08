[TOC]



# 用TypeScript编写发布函数库

> 作者简介 翎刀 蚂蚁金服数据前端

## 为什么使用TypeScript编写函数库

TypeScript作为一种有类型定义的JavaScript超集语言，用来写函数库除了给自己开发者自己带来如开发效率提升、静态检查等好处外，对库的使用方也能带来一下好处：

- 对于直接使用JavaScript的使用者，通过TypeScript的编译过程，可以生成直接使用的JavaScript代码，如ES5版本的JavaScript代码，对使用者的使用没有影响
- 对于使用TypeScript的开发者，通过TypeScript编译生成的定义文件，能极大提升使用者的使用体验

## 函数库的编译控制

代码库在发布之后，使用者可能期望能以各种方式来使用库，如直接在浏览器中加载使用、通过NodeJs的CommonJS模块方式来引用代码库、或者直接通过ES6的module方式来引用。

在使用TypeScript编写代码库后，开发者可以不用关心代码库使用者的饮用方式，而直接使用TypeScript的模块机制来模块化编写代码库；而是在通过TypeScript的编译过程来生成

下面分别介绍以上几种不同使用情景下的编译过程。

## CommonJS模块化代码的生成

目前前端开发库绝大部分都会发布到npm上，npm作为NodeJs的包管理器，提供CommonJs的模块化代码是非常有必要的。

通过tsconfig.json来配置CommonJS模块代码的生成：

```
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "moduleResolution": "node",
    "outDir": "dist/cmjs",
    "rootDir": "./src",
    "declaration": true,
    "sourceMap": true,
    "lib": ["dom", "es6"]
  }
}
```

通过以上配置， `"module": "commonjs"` 来申明编译生成目标代码模块为`commonjs`，生成目标代码目录为：`"outDir": "dist/cmjs"`。

同时在`package.json`中，通过`main`字段来申明CommonJS的入口文件：

```
{
      "name": "myLib",
      "version": "0.1.0",
    "main": "dist/cmjs/index.js",
}
```

## ES6模块代码的生成

随着ES6标准的流行，以及各种打包工具对ES6模块的原生支持，如webpack的[resolve.mainFields配置](https://link.zhihu.com/?target=https%3A//webpack.js.org/configuration/resolve/%23resolve-mainfields)，提供ES6模块代码能够让使用者享受ES6模块的一些特性。

同CommonJS一样，也可以通过配置tsconfig.json的方式来生成ES6模块代码，但一般在tsconfig.json中使用commonjs作为默认配置，所以可以在package.json中通过添加script来通过TypeScript编译器命令行参数来编译生成ES6模块代码：

```
{
    "name": "myLib",
      "script": {
        "build:cmjs": "tsc -P tsconfig.json",
          "build:es6": "tsc -P tsconfig.json --module ES6 --outDir dist/es6",
          "build": "npm run build:cmjs; npm run build:es6"
    }
}
```

其中 `build:cmsj` 编译生成CommonJS模块目标代码，`build:es6`编译生成ES6模块目标代码。对于ES6模块的代码，通过在`package.json`中的module字段来申明ES6模块代码的入口文件，以便能够识别ES6模块的模块加载器使用：

```
{
      "name": "myLib",
      "version": "0.1.0",
    "main": "dist/cmjs/index.js",
     "module": "dist/es6/index.js",
}
```

## 能够直接给浏览器使用的代码生成

生成能够直接给浏览器使用的代码，能够方便使用者，不需要使用包管理器，直接在html文件中引用。如React，可以直接在HTML文件中引入dist/react.js单独文件。对此，需要对模块化分布的代码按依赖合并大包，所以使用打包工具如webpack完全可以做到，这里不仔细介绍。

另外，还可以可以借助[Browserify](https://link.zhihu.com/?target=http%3A//browserify.org/)工具来将上述编译生成的CommonJS模块化代码整体打包成可供浏览器直接使用的代码。同样，通过简单的添加package.json的script来完成：

```
{
    "name": "myLib",
      "script": {
        "build:cmjs": "tsc -P tsconfig.json",
          "build:es6": "tsc -P tsconfig.json --module ES6 --outDir dist/es6",
          "build:web": "browserify dist/cmjs/index.js --standalone myLib -o dist/web/bundle.js",
          "build": "npm run build:cmjs; npm run build:es6"
    }
}
```

## 版本与发布

## 语义化版本

语义化版本使用如：主版本号.次版本号.修订号 的版本格式，有详细严格的[定义文档](https://link.zhihu.com/?target=http%3A//semver.org/lang/zh-CN/)。遵循语义化版本号规则有利于使用者理解代码库的升级修改，共同遵循语义化版本号能使开发者和使用者共同获益。

此外，文档中还提到了一些常见的问题，如：

- 在函数库开发阶段，如果控制版本号？推荐是从0.1.0版本开始
- 如何判断发布1.0.0的时机？软件被应用在正式环境；有固定的API被使用者依赖；存在向下兼容问题的时候
- 对于公共API，即使是最小但向下不兼容的改变都需要产生新的主版本号，岂不是很快就到了如42.0.0版？这是开发的责任感和前瞻性的问题，为什么有这么多不兼容更改：）

## 发布版本

在TypeScript编写函数库后，需要更新版本时候，推荐使用`npm version`命令来更新版本号，如：

- 更新修订号：`npm version patch`
- 更新此版本号：`npm version minor`
- 更新主版本号：`npm version major`

执行上述命令，会增加相应的版本号保存到package.json的version字段中。在使用git做源码版本控制的时候，还可以添加-m参数来自动成来提供一条版本更新commit记录和tag记录，如运行`npm version patch -m "update version %s"`除了更新package.json中的version字段外，还会自动生成一条commit记录，commit message中的`%m` 会被替换成新生成的版本号，此外还有自定生成tag记录。

 



https://github.com/ProtoTeam/blog/blob/master/201711/4.md 

 