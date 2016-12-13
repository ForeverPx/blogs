# Nodejs连接redis遇到的坑

目前Nodejs端使用redis模块对redis服务进行连接，代码如下：

```js
var redisClient = redis.createClient(config.redisAuth);

redisClient.on("error", function (err) {
    redisClient.quit()
    logger.error("Error " + err);
    redisClient = redis.createClient(config.redisAuth);
});

module.exports = redisClient;
```

当redis发生error的时候，首先是quit了这个连接，然后再创建一个新的连接。
这样写出现了一个问题，就是当发生error然后连接quit了之后，该连接并没有像预想中的去重新创建新连接去连接，
所以之后使用 redisClient 去set或者get的时候就会报错，此时redis的操作无法再正常进行，除非重启服务器重置连接。
当初这么写是为了保证redis链接在异常情况下保证能重连，现在看来这种方式是有问题的。

查看官方文档，确定redis库是有完善的自动重连机制的，所以以后这块我们就不做处理了。

```js
redisClient.on("error", function (err) {
    logger.error("Error " + err);
});
```

但并不能保证就一定没问题了，所以这里准备加入redis错误告警，对redis异常进行监控。

```js
redisClient.on("error", function (err) {
   report('RedisError');
   logger.error("Error " + err);
});

var client = redis.createClient({
    retry_strategy: function (options) {
        if (options.error.code === 'ECONNREFUSED') {
            // End reconnecting on a specific error and flush all commands with a individual error 
            report('连接被拒绝');
        }
        if (options.times_connected > 10) {
            report('重试连接超过十次');        
        }
        // reconnect after 
        return Math.max(options.attempt * 100, 3000);
    }
});
```

在redisClient初始化时，加入 retry_strategy 配置， 对连接的异常进行处理。

此方法如果返回 数字 类型比如 1000， 则该连接 会在 1秒后重新连接。如果返回非数字类型或者是 Error类型，则中断重连！！（所以必须返回数字）。

这里还有一个值得注意地方,
当redis断开连接或者连不上的时候，会抛出一个异常，如果nodejs程序没有显示捕获这个异常进行处理，那么nodejs进程就会退出，所以需要如下类似的代码才能保证redis断开后重连。

```js
function uncaughtExceptionHandler(err){
    if(err && err.code == 'ECONNREFUSED'){
        //do someting
    }else{
        process.exit(1);
    }
}
process.on('uncaughtException', uncaughtExceptionHandler);
```