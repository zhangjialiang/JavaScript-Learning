#ReactJs学习体会#

###一. 相关介绍和传送门###
reactjs是facebook公司开发的用于构建界面ui的javascript库，其主要特点包括：jsx语法，virtual DOM，单向数据绑定等。
-[GitHub地址](https://github.com/facebook/react)
如果想学习ReactJs的基本语法和用法，请点击下面的文档链接。*在官方文档中有完整的教程教你如何用reactjs构建一个简单的web项目的view*
-[文档地址--English](http://facebook.github.io/react/docs/tutorial.html)      	-[文档地址--中文](http://reactjs.cn/react/docs/getting-started.html)
reactjs还有两个衍生项目：
-[react-native](https://github.com/facebook/react-native)用reactjs写app
-[react-canvas](https://github.com/Flipboard/react-canvas)用canvas代替dom作为ui，使得移动端界面展示更为流畅

###二. 构建DOM###
reactjs使用其特有的jsx语法构建dom结构，下面用一个简单的例子介绍其语法。
假设我们要构造如下的一个dom结构
`<a class="link" href="https://www.baidu.com">link</a>
`
在原生DOM中，我们可以通过js构造该dom结构
```
var a = document.createElement('a');
a.setAttribute('class', 'link');
a.setAttribute('href', 'https://www.baidu.com');
a.innerHTML = 'link';
```
或者通过在js中构造dom结构字符串来创建，但是众所周知这种方式会使得代码阅读维护困难，因此造了很多前端模板的轮子来解决动态创建dom结构的问题。
```
<script type="text/template" id="a">
	<a class="link" href="https://www.baidu.com">link</a>
</script>
var a = Template.render(document.getElementById('a'));
```
上述例子中的模板语言可以与js相互分离，而在reactjs中通过jsx构建dom结构是混杂在js中，这种设计是否会背离分离html,js,css的理念呢？我们来看一下reactjs实现这个dom结构的例子：
```
var Link = React.createClass({
		render: function () {
				return <a className='link' href='https://www.baidu.com'>link</a>
		}
});
```
这个Link类就是a标签进过封装后的呈现，如果要在html中加入这个a标签，只需要调用以下语句：
`React.render(<Link />,document.getElementById('div'));
`
React.createClass中render方法return的的结构就是jsx语法，由于其是混合在js中，所以js中的保留字它并不能使用，class需要使用className替代。另外由于return的语法特点，React.createClass生成的类构建的dom结构必须有且只有一个元素包裹，某些情况下会增加dom结构复杂度。
从上面的例子我们可以看出ReactJs这种构建dom结构的方式与web component有着很多共通点，可以认为是一种web component的实现，通过class中生命周期相关函数，ReactJs能够轻松构建被反复操作重用的web组件。
###三. React.createClass的特点###
reactjs中有丰富的生命周期回调函数：
```
componentWillMount: 初始化渲染前调用
componentDidMount: 初始化渲染后调用
componentWillReceiveProps： 接受新props时调用
shouldComponentUpdate：接受新props或state时调用，返回值true/false决定是否更新视图
componentWillUpdate: 在接收到新的 props 或者 state 之前立刻调用。在初始化渲染的时候该方法不会被调用
componentDidUpdate：在组件的更新已经同步到 DOM 中之后立刻被调用
componentWillUnmount: 在组件从 DOM 中移除的时候立刻被调用
```
通过上述的生命周期函数，reactjs能够很好得操作构建component。在createClass构建的component中，有两种数据集合：props和state，虽然他们的内容很相似，甚至可以相互替代，但是他们扮演着不同的角色。
#####props vs state#####
######共识######
* props和state都是简单js对象
* props和state发生变化都会触发render update，如果你不在shouldComponentUpdate中做出设定的话，两者的变化都会导致dom刷新，因此为了提高性能，可以在shouldComponentUpdate指定刷新产生条件
* props和state是确定性的，如果组件中的state和props是一致的，那么render产生的结果也应该是一致的

######props######
* props即dom元素的properties，他是一个组件的configuration，他应该是不可变的，所以如果在组件运行过程中，你要修改组件的属性，或者从服务器取值加入组件中，应该修改组件的state。父组件通常会在创建子组件的时候为子组件的props赋值。

######state######
* state应该是可序列化的数据，通常会在getInitialState中设置state的初值。与props不同，props由于在初始化时使用，经常会包含回调函数。
* state应该是私有的，一般不会在组件间传递。

如果你想了解更多的关于props和state的特点，可以浏览[props vs state](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)

###四. ReactJs 的Virtual DOM###
React.createClass的render函数会调用React.createElement来构建dom对象，而React.createElement并没有直接调用js来构造dom，他额外的构造了一种数据结构，就是标题所指的virtual dom。因此使用reactjs构造操作组件，实质上我们只是在操作数据，而具体dom的结构的操作由reactjs通过自己的dom diff算法来对比数据集发生的变化，按最小粒度来更新视图。这种更新视图的方法一方面提高了刷新效率，另一方面使得UI层完全可替换，因此才有了衍生的react native。
######diff 算法######
reactjs通过比较数据发生变化后dom树的差异来更新视图，如果是单纯的比较两颗树的差异的话，时间复杂度是O(n^3),reactjs通过一种简单的技巧，使得diff复杂度达到O(n)：

* 在一般的情况下我们如果修改dom元素的话，之后在dom树结构的某一层修改它，例如修改它的内容，或者在他前后加元素，而不会移动它的层级位置，reactjs据此通过对比同层元素降低复杂度。
* 你可以为组建增加一个key属性来帮助reactjs处理对应关系。
* 在reactjs中通常你会构建自己的组建来创建dom，reactjs会根据组建的类名来匹配。

######事件代理######
reactjs的事件代理与jquery的事件委托机制原理类似，将事件的listener绑定到根节点，将listener存储到hashmap中，并且通过依靠component各自独立的id来编码时间传播层级提高了处理速度，也节省了内存

######渲染机制######
* 批量渲染：你调用 component 的 setState 方法的时候，React 将其标记为 dirty。到每一个事件循环结束，React 检查所有标记 dirty 的 component 重新绘制。

*详细的diff算法的参见：[React diff algorithm](http://calendar.perfplanet.com/2013/diff/)*

###五. ReactJs的不足###
* 只处理了web的ui层,涉及数据的话需要其他库，官方推荐的flux学习成本较高。
* 没有Promises
* 不支持iE8一下，以后也不可能会
* 相比较提供的功能，类库体积过大
* 没有提供可复用的组建，不适合快速开发
* 组件间通信困难，非父子级组建通信只能通过全局事件