
#Nodejs mkdirP 模块导致CPU占用高的问题

近期将nodejs项目部署到服务器上并启动时，发现node进程的cpu占用率在40%左右，当时表示非常不解，刚启动的服务并没有运行什么需要大量消耗cpu的逻辑，且此时还未有请求发送到服务器端。

鉴于这种情况，只能猜测是某段程序在初始化一些东西的时候异常，所以才导致了这种情况。

经过对代码的排查后，最终锁定出为题的代码块如下：
```js
router.use(multer({
    dest: config.uploadDir,
    limits:{
        fileSize : config.fileSizeLimit, //bytes , == 50M
        files : 1
    },
    rename:function(fieldname, filename) {
        return uuid.v1() + "_" + filename + "_" + Date.now();
    },
    onFileUploadStart: function (file) {
        console.log(file.originalname + ' is starting ...');
    },
    onError: function (error, next) {
        winston.error(error);
        next(error);
    }
}));
```
初看这段代码怎么也不相信它会占用这么高的CPU，所以最大的嫌疑就是multer模块的初始化了，于是进入源码看看。

multer初始化部分源码：
```js
  var dest;

  if (options.dest) {
    dest = options.dest;
  } else {
    dest = os.tmpdir();
  }

  mkdirp(dest, function(err) { if (err) throw err; });
  
  var rename = options.rename || function(fieldname, filename) {
    var random_string = fieldname + filename + Date.now() + Math.random();
    return crypto.createHash('md5').update(random_string).digest('hex');
  };
```

这段代码最有嫌疑的不是mkdirP（创建目标文件夹）就是crypto的md5了，那么一个一个排除。首先只是注释掉mkdirP，重启node，发现cpu基本为0了，所以能断定问题出在mkdirP上了。

单凭这句**mkdirp(dest, function(err) { if (err) throw err; });**也看不出什么东西，所以还是得继续进入mkdirP的实现。

mkdirP在创建文件夹的源码中有这么一段：

```js
function mkdirP (p, mode, f, made) {
	//此处省略几行
	fs.mkdir(p, mode, function (er) {
        if (!er) {
            made = made || p;
            return cb(null, made);
        }
        switch (er.code) {
            case 'ENOENT':
                mkdirP(path.dirname(p), mode, function (er, made) {
                    if (er) cb(er, made);
                    else mkdirP(p, mode, cb, made);
                });
                break;
            default:
                fs.stat(p, function (er2, stat) {
             
                    if (er2 || !stat.isDirectory()) cb(er, made)
                    else cb(null, made);
                });
                break;
        }
    });
```

看到case 'ENOENT'时就有点端倪了，在创建文件夹失败后，又调了mkdirP，这样如果第一次出问题了，那不就不停的尝试创建了。如果是这样，那么肯定是传入的路径出了问题。回头检查config里面的路径时，发现路径在部署配置的时候斜杠的方向写反了：
```js
config.path = 'c:\aa\aa';
```
正确的应该是：
```js
config.path = 'c:\\aa\\aa';
or
config.path = 'c://aa//aa';
```
几经周折终于解决了问题，nodejs的路还很长，走，吃饭去……

文章作者：forevercjl
转载请注明出处。












