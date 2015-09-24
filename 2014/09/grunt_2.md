#Grunt入门教程之二

##Concat插件
concat是grunt中用来做文件连接的常用插件，比如说你写了一个类库，有三大模块，如：
    a.js
    b.js
    c.js
当你的项目准备发布的时候，你可能需要将这三个模块合并成一个大的模块all.js，这样做可以减少HTTP请求，增快页面的响应速度。
如果我们每次发布的时候又要连接这三个模块，并测试all.js，确保大模块无BUG之后再发布，就显得很蛋疼了。一种好的方式是，每当你修改了其中一个小模块，他都会自动连接成all.js，并且你的项目在开发的时候也是引用all.js,
那就可以避免上面的问题了。
这个时候我们就需要一个自动连接js文件的自动化工具，基于grunt的话，那就是concat插件了。
下面介绍一下使用concat的几种形式

1.多个文件合并成一个文件
```js
grunt.initConfig({  
  concat: {  
    options: {  
      separator: ';',  
      stripBanners: true,  
      banner: '/*! hello - v1.2.3 - 2014-2-4 */'  
    },  
    dist: {  
      src: ['a.js', 'b.js', 'c.js'],  
      dest: 'all.js',  
    },  
  },});
```
- options中是一些选项，键值对方式，可以做一些简单的配置
- separator 文件连接分隔符，表示连接的文件用指定的separator分割。
- banner 出现在合并后的文件开头出现，一般做说明和注释用。
- footer 出现在合并后的文件底部出现，一般做说明和注释用。
- stripBanners 如果为true，去除代码中的块注释，默认为false
- process  如果为true，则在合并前先执行。
- src是一个数组，里面的元素是你要合并的文件，按0-n的顺序进行合并。（注意你要合并的顺序）
- dest是你生成合并文件的目录和文件名。
    
2.多个合并任务
```js
grunt.initConfig({  
  concat: {  
    one: {  
      src: ['a.js'],  
      dest: 'all.js',  
    },  
    two: {  
      src: ['b.js', 'c.js'],  
      dest: 'all-sec.js',  
    },  
  },});  
```
这里分别定义了两个合并任务，one和two，分别生成两个合并文件
grunt concat:one 执行第一个任务， grunt concat:two 执行第二个，如果只是  grunt concat，则两个任务都会执行。
下面是另一种写法，效果同上
```js
grunt.initConfig({  
  concat: {  
    basic_and_extras: {  
      files: {  
        'all.js': ['a.js'],  
        'all-sec': ['b.js', 'c.js'],  
      },  
    },  
  },});  
```

3.动态的文件名
```js
grunt.initConfig({  
  pkg: grunt.file.readJSON('package.json'),  
  concat: {  
    dist: {  
      src: ['a.js'],  
      dest: 'dist/<%= pkg.name %>-<%= pkg.version %>.js',  
    },  
  },});  
```

以上是concat插件最基本和常用的用法，如果还是不明白的话可以到concat的github主页去学习学习
[https://github.com/gruntjs/grunt-contrib-concat](https://github.com/gruntjs/grunt-contrib-concat)

文章作者：foreverpx