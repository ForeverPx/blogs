#项目介绍
`nightmare`是一个高级的浏览器自动化代码库。
它的主要目标是使用一些简单的方法就能自动的模拟用户日常在浏览器中的操作。
比如你平时打开浏览器，然后在搜索框中输入内容，再点击搜索按钮进行搜索。
`nightmare`能自动执行上述步骤，并拿到执行结果，也就是搜索结果。
通过它可以轻松实现自动化测试。

#示例
以搜索雅虎为例：
```js
    var Nightmare = require('nightmare');
    yield Nightmare()
      .goto('http://yahoo.com')
      .type('input[title="Search"]', 'github nightmare')
      .click('.searchsubmit');
```

我们可以利用它来做一些自动化测试：
```js

    var Nightmare = require('nightmare');
    var expect = require('chai').expect;
    
    describe('test yahoo search results', function() {
      it('should find the nightmare github link first', function*() {
        var nightmare = Nightmare()
        var breadcrumb = yield nightmare
          .goto('http://yahoo.com')
          .type('input[title="Search"]', 'github nightmare')
          .click('.searchsubmit')
          .wait('.url.breadcrumb')
          .evaluate(function () {
            return document.querySelector('.url.breadcrumb').innerText;
          });
        expect(breadcrumb).to.equal('github.com');
      });
    });
```

通过上面的代码我们可以自动判断搜索的结果是否符合我们预期。
在我们的日常开发中，可以利用这个工具对我们的交互逻辑进行自动化测试。

#项目主页
更多的API和文档可以在`nightmare`的主页中查看：
[1](https://github.com/segmentio/nightmare)