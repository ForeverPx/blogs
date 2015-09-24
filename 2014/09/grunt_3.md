#Grunt入门教程之三

##Yuidoc插件
Yuidoc 是一个可以将代码注释生成HTML格式文档的工具，文档的生成完全基于JavaDoc风格的代码注释规则。
我们在写公共模块或者API的时候，往往会输出文档给调用者查看，这时候Yuidoc就是一个很好的助手。
下面介绍一下如何在Grunt中使用Yuidoc。
```js
grunt.initConfig({
  pkg: grunt.file.readJSON('package.json'),
  yuidoc: {
    compile: {
      name: '<%= pkg.name %>',
      description: '<%= pkg.description %>',
      version: '<%= pkg.version %>',
      url: '<%= pkg.homepage %>',
      options: {
        paths: 'path/to/source/code/',
        themedir: 'path/to/custom/theme/',
        outdir: 'where/to/save/docs/'
      }
    }
  }
});
```

compile中前四个属性比较容易理解，分别为“名字”，“描述”，“版本”，“项目主页URL”。
options中的属性跟yuidoc本身的属性有关，不局限于示例代码中的三个，更多的属性请参见Yuidoc文档

- path: 代码源文件存放路径
- themedir：生成的HTML样式的存放路径
- outdir：生成的HTML doc文档的存放路径

[grunt-contrib-yuidoc](https://www.npmjs.org/package/grunt-contrib-yuidoc)

文章作者：foreverpx

