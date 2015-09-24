#Grunt入门教程之一

​grunt是一个强大的自动化构建工具，它能自动的执行你自定义的任务，帮助你编译、压缩、单元测试等。它可以极大的减轻作为程序员的工作量。一切无聊枯燥且重复的工作就交给grunt来完成吧！
​grunt中有非常多的插件，有官方的可以直接使用，如果你觉得这还不够，你可以自己亲自写一个插件，将其发布到npm上。
​常用的grunt插件有：
​- Less
- handlebars
- jade
- JS Hint
- watch
​下面我们就以less为例，来看看如何使用grunt来把less编译为css并压缩。
​首先我们要通过npm安装grunt命令行（CLI）到全局变量中
```bash
npm install -g grunt-cli  
```
​CLI安装完成后，就可以建一个grunt项目来跑了。
​新建一个grunt文件夹，在grunt中新建package.json和Gruntfile.js文件。
​package.json为配置信息，里面可以配置需要导入的插件和一些变量或常量。
```js
{      
      "name": "XXX",  
      "version": "0.1.0",  
      "devDependencies": {  
        "grunt": "~0.4.2",  
        "grunt-contrib-jshint": "~0.6.3",  
        "grunt-contrib-nodeunit": "~0.2.0",  
        "grunt-contrib-uglify": "*",  
        "grunt-contrib-less": "*",  
        "grunt-contrib-concat":"*",  
        "grunt-contrib-yuidoc": "*",  
        "grunt-contrib-cssmin":"*",  
        "grunt-contrib-watch":"*"  
      }  
    }  
```
​Gruntfile.js为任务的配置，可以理解为程序的主入口。

```js
module.exports = function(grunt) {  
        grunt.initConfig({  
            less: {  
                compile: {  
                    files: {  
                        'a.less'  
                    }  
                }  
            },  
            cssmin:{  
                minify: {  
                    expand: true,  
                    src: ['a.css'],  
                    dest: '',  
                    ext: '.min.css'  
                }  
            },  
            watch: {  
                css: {  
                    files: ['/*.less'],  
                    tasks: ['less','cssmin']  
                }  
            },  
        });  
        grunt.loadNpmTasks('grunt-contrib-less');  
        grunt.loadNpmTasks('grunt-contrib-cssmin');  
        grunt.loadNpmTasks('grunt-contrib-watch');  
          
        grunt.registerTask('default', ['less',"cssmin","watch"]);  
    };  
```
​在当前目录下，命令行输入 grunt 就能开始执行 less,cssmin,watch任务。
​下面讲解一下上面的代码。
​首先我们配置了less插件，并指定将a.less编译成a.css并保存在当前的目录，接着是配置了cssmin插件，将上一步生成的a.css文件进行压缩，生成a.min.css文件。我们需要的就是最终生成的那个a.min.css文件。
​下面来看一下神奇的watch插件。我们在项目开发中，不可能每修改一次a.less，都手动去执行命令grunt。所以我们需要一个监听文件改变的东东，它就是watch。我们在配置中，用watch插件监听当前目录下，所有的less文件，只要有改动，就会再次执行 less 和 cssmin插件。是不是很方便？
​上面是使用grunt最简单的一个例子，后面的文章会对grunt的更多特性以及插件做更详细的讲解。

文章作者：forevercjl