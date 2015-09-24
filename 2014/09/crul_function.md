#柯里化函数之Javascript

##定义
根据定义来说，柯里化就是将一个接收“多个”参数的函数拆分成一个或者许多个接收“单一”参数的函数。定义看起来是比较抽象的，下面来举个例子：

##代码
```js
function concat(str1,str2){
	return str1 + str2;
}
concat("forever","px") // "foreverpx"
```

不难理解，上面的代码中定义了一个连接字符串的函数，返回连接两个传入参数的字符串。现在我们再看看另一种定义方式：
```js
function concatCurry(str1){
	return function(str2){
		return str1 + str2;		
	}
}
var concatOne = concatCurry("forever");
concatOne("px"); //foreverpx
```
上面的代码中，我们将一开始传入两个参数的函数改装成了一个（或者说是两个）只接收一个参数的函数。
我们来创建一个将接受多个参数的函数柯里化的通用函数：
```js
function curry(fn) {
    var args = [].slice.call(arguments, 1);
    return function() {
        return fn.apply(null, args.concat([].slice.call(arguments, 0)));
    }
}
var concatOne = curry(concat,"forever");
concatOne("px"); //foreverpx
```

柯里化的过程利用了闭包，使得返回的函数也能获得第一次传入的参数。
首先将传入的第二个参数存入args变量中，然后返回一个函数，在返回的函数中，将第一次传入的参数与柯里化之后传入的参数拼接起来成一个数组，然后通过apply方法调用柯里化之前的函数（fn)，这样就达到了柯里化的效果。
如果是三个参数的函数想要柯里化，则如下：
```js
function concat(s1,s2,s3){
	return s1+s2+s3;
}
var cur1 = curry(concat,"forever");
var cur2 = curry(cur1,"px");
cur2("-me"); //foreverpx-me
```

文章作者：foreverpx