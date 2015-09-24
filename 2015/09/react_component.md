#React，用组件化思想写前端代码

前阵子尝试用React开发了一个项目的前端，写起来还算是流畅。将页面中各模块进行分割并形成组件之后，管理起来更加的方便，代码的可读性也相对于传统的面条式编程有很大的提高。React相对于Angular来说，我个人认为是更加轻量化的，它更注重于MVC中的V。

举个例子，开发中常常会用到button这个元素，我们会给button加上样式、行为等等。一个项目中button可能是被经常重复使用的，他的样式也可能几乎是一摸一样的，无非是color和size的区别而已。按照传统的写法，可能代码是下面这个样子的：
```html
<button onClick='function(){alert('clicked!')}' class='btn btn-green btn-large'>点我</button>
```
如果click事件是有默认事件的，class默认也是绿色，那么每次都写这一堆是有点烦了，如果我们将这个button视为一个组件，那么我们可以给它设置默认的样式和行为，并通过传不同的值给组件改变它的状态。我们来个React版本的。

```js
var Button = React.createClass({
    propTypes:{
        children: React.PropTypes.any,
        className: React.PropTypes.string,
        disabled: React.PropTypes.bool,
        onClick: React.PropTypes.func,
        style: React.PropTypes.object,
        type: React.PropTypes.oneOf(['submit', 'button'])
    },
    getInitialState:function(){
        return {
            disabled:this.props.disabled,
            className:'btn-green',
            type: this.props.type ? this.props.type : 'button',
            text: null
        }
    },
    componentWillReceiveProps:function(newProps){
        if (newProps.disabled !== this.props.disabled) {
            this.setState({ disabled: newProps.disabled })
        }
    },
    text:function(text){
        this.setState({
            text:text
        });
    },
    handleClick:function() {
        if (this.props.onClick) {
            this.props.onClick()
        }
    },
    render:function(){
        return (
            <button className={this.state.className}
                    disabled={this.state.disabled}
                    type={this.state.type}
                    style={this.props.style}
                    onClick={this.handleClick.bind(this)}>
                {this.state.text || this.props.children}
            </button>
        );
    }
});
```
这里Button就视为一个模块或者组件，它接收父组件的值来改变它的默认状态，当没有值传入时，使用默认的内部值。
为了达到最开始那个button的效果，我们可以这么使用它：
```
React.render(
       <div>
           <Button size='large' onClick='function(){alert('click')}'>
       </div>
       , document.getElementById('container'));
```
虽然这样看上去好像并没有和传统方式有太大的区别，而且还多了一堆代码去定义这个button组件，得不偿失，但是这仅仅只是组件化的开始，面对更复杂的组件时，它的好处就体现出来了。看看下面的例子：

需求是我们需要一个搜索框，在用户输入的同时通过异步请求进行搜索，同时为了防止用户输入速度快导致请求过于频繁，在用户输入每个字后延迟300ms进行搜索，如果在这段时间内用户任然有输入，那么就再延迟300ms，也就是高频防抖。

以传统的方式来，我们得先写个input，然后把这个input包装成search input的样子，然后注册事件，并在事件中设置延迟函数，然后才真正执行搜索动作。。。等另一个页面又有搜索时，重复刚才的步骤。如果把SearchInput设想成一个组件，那么它会是下面这个样子的。

```js
var SearchInput = React.createClass({
    inputTimeout:null,
    propTypes:{
        size: React.PropTypes.string,
        onInput: React.PropTypes.func,
        delay: React.PropTypes.number,
        disabled: React.PropTypes.bool
    },
    getInitialState:function(){
        return {
            size: this.props.size ? this.props.size : '',
            delay: this.props.delay ? this.props.delay : 300
        }
    },
    handleInput: function(event){
        var value = event.target.value;
        if(this.inputTimeout){
            window.clearTimeout(this.inputTimeout);
        }
        var that = this;
        this.inputTimeout = setTimeout(function(){
            that.props.onInput(value);
        },this.state.delay);
    },
    render: function(){
        var sizeClasss = this.state.size ? "topcoat-search-input--"+this.state.size :
            "topcoat-search-input";
        return (
            <input type="search"
                   placeholder='search'
                   onInput={this.handleInput.bind(this)}
                   className={sizeClasss}
                   disabled={this.props.disabled}
                />
        );
    }
});
```
我们将延迟的时间和是否禁用等信息当做属性传给SearchInput组件，并将延迟操作的逻辑等封装在组件内部，以后我们就不用关心这些了，直接使用即可。

```
React.render(
       <div>
           <SearchInput delay=500 onClick='function(value){alert(value)}'></SearchInput>
       </div>
       , document.getElementById('container'));
```
我们传入delay与搜索逻辑函数即可，input的value也会传入搜索逻辑函数。

当然，还有更复杂的情况，我们可以将逻辑封装在组件中，使用的人只需要通过属性改变组件的状态，而不需要关心组件状态改变的逻辑。

最近尝试封装一些通用的React UI组件，挑选了Topcoat样式去封装，感兴趣的朋友可以点击下面这个链接
[https://github.com/ForeverPx/react-topcoat](https://github.com/ForeverPx/react-topcoat)

原文链接：[https://github.com/ForeverPx/blogs/blob/master/2015/09/react_component.md](https://github.com/ForeverPx/blogs/blob/master/2015/09/react_component.md)
转载请注明出处。