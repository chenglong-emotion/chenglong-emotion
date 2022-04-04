##  React

###  核心概念

####  事件处理

将触发事件的函数通过{}包裹，且不需要括号，只需写函数名

使用箭头函数定义或与当前实例绑定bind()

####  条件渲染

可以使用if进行判断  可以返回jsx语法

可以使用变量来保存jsx语法，或者函数，其返回值为jsx语法

可以使用&&运算符，左边若为false则不会渲染右边

三目运算符

可以通过返回null来使组件隐藏

####  列表和key

map()方法遍历，每次遍历可以返回一个元素，且每个元素分配一个key,key可以来源于遍历对象，也可以来源于函数的第二个参数index,默认生成的，

key只是在兄弟之间为一，不需要全局唯一

只会传递给react组件，不能给自己的组件，

可以在jsx中使用map()

####  表单

受控组件

将state于组件的value绑定

select标签	可以通过select标签的value来控制子项选择与否

注意：file类型的input标签非受控，因为它的值是文件内容，只读

当处理多个Input输入时，可以通过添加name属性

####  状态提升

将共享状态提升至父组件

可以将父组件的函数传递给子组件，子组件调用可以改变父组件的state

####  组合 vs 继承

组件的children,是组件的props的一种

也可以自行定义，将所需内容传递进props中

####  react中的思考

数据单向绑定

数据流从上至下

通过向子组件传递函数，来回调，改变state

###  高级指引

#### 无障碍辅助功能

无障碍表单

事件触发器，鼠标的点击事件，window的点击事件，失去焦点事件onBlur()、获得焦点事件onFocus()

####  代码分割

文件打包

动态import()语法

```react
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

React.lazy  能够处理动态引入的组件

```react
function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
```

​	fallback用于组件未加载出来显示的内容，可以放置任何组件，`Suspense` 组件中渲染 lazy 组件，如此使得我们可以使

​	用在等待加载 lazy 组件时做优雅降级

#####  基于路由的代码分割

在路由切换时分割代码

```react
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
```

####  上下文context

组件之间共享数据，不必需要props传递大量数据，避免中间元素传递props

#####  使用

```react
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');
class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。    
      // 无论多深，任何组件都能读取这个值。    
      // 在这个例子中，我们将 “dark” 作为当前的值传递下去。    
      return (
      <ThemeContext.Provider value="dark">        
              <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar() {  
    return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。  
    // React 会往上找到最近的 theme Provider，然后使用它的值。  
    // 在这个例子中，当前的 theme 值为 “dark”。 
    static contextType = ThemeContext;
  render() {
    return <Button theme={this.context} />;  }
}
```

通过一个const ThemeContext = React.createContext('light');来创建context，其实就是在组件最顶层维护一个数据变量

#####  API

React.createContext

Context.Provider

Class.contextType

```react
MyClass.contextType = MyContext;
```

可以在任何声明周期中获取this.context

Context.displayName

context 对象接受一个名为 `displayName` 的 property，类型为字符串。React DevTools 使用该字符串来确定 context 要显示的内容。

动态context,即将context保存在另一个文件中

通过context传递一个函数

context.provider 可以设置共享数据

context.consumer  可以消费provider提供的数据，若没有嵌套在provider下面，则只能获取到默认数据，需要通过回调获

取数据，数据在参数里面

消费多个context

为了防止consumer中的组件被意外渲染，可以将共享数据放在顶层组件的state中

####  错误边界

错误边界是react的一种组件，可以捕获其子组件任何位置的js错误，并且渲染出备用UI.

在渲染期间、生命周期方法、和整个组件树得到构造函数中捕获错误、

```java
注意

错误边界无法捕获以下场景中产生的错误：

事件处理（了解更多）
异步代码（例如 setTimeout 或 requestAnimationFrame 回调函数）
服务端渲染
它自身抛出来的错误（并非它的子组件）
```

#####  使用

当一个组件定义了[`static getDerivedStateFromError()`](https://react.docschina.org/docs/react-component.html#static-getderivedstatefromerror) 或 [`componentDidCatch()`](https://react.docschina.org/docs/react-component.html#componentdidcatch) 这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界。当抛出错误后，请使用 `static getDerivedStateFromError()` 渲染备用 UI ，使用 `componentDidCatch()` 打印错误信息。

```react
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {    
      // 更新 state 使下一次渲染能够显示降级后的 UI    
      return { hasError: true };  }
  componentDidCatch(error, errorInfo) {    
      // 你同样可以将错误日志上报给服务器    
      logErrorToMyService(error, errorInfo);  }
  render() {
    if (this.state.hasError) {      
        // 你可以自定义降级后的 UI 并渲染      
        return <h1>Something went wrong.</h1>;    }
    return this.props.children; 
  }
}
```

错误边界只能捕获其子组件的错误，无法捕获自身的错误，当一个错误边界无法渲染错误信息时，会将错误传递到上一个

最近的错误边界。

错误边界无法处理事件处理中的异常，若有需要，则使用try...catch...

####  Refs转发

将组件传递到子组件

就是将React.creactRef创建的ref指向一个dom节点，就可以通过这个ref来操控节点

转发ref

```react
const FancyButton = React.forwardRef((props, ref) => (  
    <button ref={ref} className="FancyButton">    
        {props.children}
     </button>
));
```

通过 React.forwardRef将ref传递到button中

```java
注意

第二个参数 ref 只在使用 React.forwardRef 定义组件时存在。常规函数和 class 组件不接收 ref 参数，且 props 中也不存在 ref。

Ref 转发不仅限于 DOM 组件，你也可以转发 refs 到 class 组件实例中。
```

####  Fragments

一个组件返回多个元素，fragments可以将子列表分组

比如一个<tr>下必须嵌套<td>,且是多个<td>,但是组件返回的标签必须是一个，若用一个<div>包含多个<td>去返回则<tr>下有<div>则无效，fragments则解决了这个问题

```react
class Columns extends React.Component {
  render() {
    return (
      <React.Fragment>        
        <td>Hello</td>
        <td>World</td>
      </React.Fragment>   
    );
  }
}
```

或者另一种写法

```react
class Columns extends React.Component {
  render() {
    return (
      <>        
        <td>Hello</td>
        <td>World</td>
      </>
    );
  }
}
```

可以像使用任何元素一样使用<></>,除了它不支持key或属性

带key的fragment

```react
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // 没有`key`，React 会发出一个关键警告
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```

####   高阶组件

参数为组件，返回值为新组件的**函数**。

组件是将props转化为UI,高阶组件是将一个组件转化为另一个组件

即将这个组件通过高阶组件这个函数增加了许多数据，成为一个新的组件，这一==这样就可以通过增加数据来产生许多种

不同的组件达到的复用

HOC不应该修改传入的组件，而应该使用组合的方式

------

约定：将不相关的props传递给被包裹的组件

HOC可以接收多个参数，也可以只接受一个

例如redux的connect函数

```react
const ConnectedComment = connect(commentSelector, commentActions)(CommentList);
```

就将一个组件变成了携带某种属性的组件了

#####  注意

不要在render中使用HOC

HOC不会把原组件的静态函数一起封装，所以解决是在HOC定义中将静态函数加上

```react
function enhance(WrappedComponent) {
  class Enhance extends React.Component {/*...*/}
  // 必须准确知道应该拷贝哪些方法 :(
  Enhance.staticMethod = WrappedComponent.staticMethod;
  return Enhance;
}
```

但要这样做，你需要知道哪些方法应该被拷贝。你可以使用 [hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics) 自动拷贝所有非 React 静态方法:

```react
import hoistNonReactStatic from 'hoist-non-react-statics';
function enhance(WrappedComponent) {
  class Enhance extends React.Component {/*...*/}
  hoistNonReactStatic(Enhance, WrappedComponent);
  return Enhance;
}
```

除了导出这个组件，还可以额外导出静态方法

ReactDOM.unmountComponentAtNode()   清除组件树相关的事件处理和其他资源

####  深入JSX

自定义组件以大写字母开头

#####  props.children

可以放字符串

React组件可以返回存储在数组中的一组元素

通过与运算符&&来判断是否渲染后面的组件

####  性能优化

生产版本的性能最优

使用脚手架的打包命令

#####  Brunch

通过安装

[`terser-brunch`]: https://github.com/brunch/terser-brunch

插件，来获得最高性能的Brunch生产构建：

#####  Rollup

为了最高效的Rollup生产环境，可以安装一些插件

为了创建生产构建，确保你添加了以下插件 **（顺序很重要）**：

- [`replace`](https://github.com/rollup/rollup-plugin-replace) 插件确保环境被正确设置。

- [`commonjs`](https://github.com/rollup/rollup-plugin-commonjs) 插件用于支持 CommonJS。

- [`terser`](https://github.com/TrySound/rollup-plugin-terser) 插件用于压缩并生成最终的产物。

  

#####  虚拟化长列表

使用虚拟滚动库 [react-window](https://react-window.now.sh/) 和 [react-virtualized](https://bvaughn.github.io/react-virtualized/) 

#####  shouldComponentUpdate

如果你的组件只有当 `props.color` 或者 `state.count` 的值改变才需要更新时，你可以使用 `shouldComponentUpdate` 来

进行检查：

```react
  shouldComponentUpdate(nextProps, nextState) {
    if (this.props.color !== nextProps.color) {
      return true;
    }
    if (this.state.count !== nextState.count) {
      return true;
    }
    return false;
  }
```

#####  不可变数据

避免该问题的最简单方式是重新生成props值或state值

比如使用concat()方法 、扩展运算符

```react
handleClick() {
  this.setState(state => ({
    words: state.words.concat(['marklar'])
  }));
}

handleClick() {
  this.setState(state => ({
    words: [...state.words, 'marklar'],
  }));
};
```

使用Object.assign()

将后一个(source)对象复制到前一个(target)对象中，没有的新增，有的替换。该方法是es语法，需要polyfill

create-react-app 默认支持

####  Portals

可以将元素放置在Dom上的任意节点上

通过portal进行事件冒泡

在父组件里捕获一个来自 portal 冒泡上来的事件，使之能够在开发时具有不完全依赖于 portal 的更为灵活的抽象。例如，

如果你在渲染一个 `<Modal />` 组件，无论其是否采用 portal 实现，父组件都能够捕获其事件。

####  Profiler

可以测量渲染的频率和开销

目的：识别出慢应用和性能（感觉像是优化的部分）

是一个组件<Profiler />

需要提供两个prop-------id和onRender

#####  onRender

```react
function onRenderCallback(
  id, // 发生提交的 Profiler 树的 “id”
  phase, // "mount" （如果组件树刚加载） 或者 "update" （如果它重渲染了）之一
  actualDuration, // 本次更新 committed 花费的渲染时间
  baseDuration, // 估计不使用 memoization 的情况下渲染整颗子树需要的时间
  startTime, // 本次更新中 React 开始渲染的时间
  commitTime, // 本次更新中 React committed 的时间
  interactions // 属于本次更新的 interactions 的集合
) {
  // 合计或记录渲染时间。。。
}
```

####  不使用es6

如果不使用es6的class，可以使用`create-react-class` 模块：

##### 显示调用bind()

```react
 // 这一行很重要！
    this.handleClick = this.handleClick.bind(this);
```

或者

```react
 // 在这里使用箭头函数就可以把方法绑定给实例：
  handleClick = () => {
    alert(this.state.message);
  }
```

####  JSX

每个 JSX 元素只是调用 `React.createElement(component, props, ...children)` 的语法糖。

####  Refs

```react
this.myRef = React.createRef(); 
render() {
    return <div ref={this.myRef} />;  }
```

当ref属性用于HTML元素时，指向DOM节点

当ref属性用于自定义组件时，则指向该组件实例

都是它们的current属性

使用Ref回调函数

```react
 this.setTextInputRef = element => {      
     this.textInput = element;   
 };

render() {
    // 使用 `ref` 的回调函数将 text 输入框 DOM 节点的引用存储到 React
    // 实例上（比如 this.textInput）
    return (
      <div>
        <input
          type="text"
          ref={this.setTextInputRef}
        />
        <input
          type="button"
          value="Focus the text input"
          onClick={this.focusTextInput}
        />
      </div>
    );
  }
```

####  Render Props

props值为一个函数

```react
<Mouse render={mouse => (
          <Cat mouse={mouse} />
        )}/>
```

render是一个prop，但他是一个函数

在Mouse中调用,并将自己的state或者其他数据传过去

就可以实现给其他组件传数据了！

由于这一技术的特殊性，当你在设计一个类似的 API 时，你或许会要直接地在你的 propTypes 里声明 children 的类型应

为一个函数。

```react
Mouse.propTypes = {
  children: PropTypes.func.isRequired
};
```

####  严格模式

可以为应用程序的任何部分启用严格模式

```react
import React from 'react';

function ExampleApplication() {
  return (
    <div>
      <Header />
      <React.StrictMode>        <div>
          <ComponentOne />
          <ComponentTwo />
        </div>
      </React.StrictMode>      <Footer />
    </div>
  );
}
```

被React.StrictMode包裹的组件会被检查

####  PropTypes

对prop类型进行限制

设置类型 PropTypes.func

设置个数 PropTypes.element.isRequired

设置默认值  

```
Greeting.defaultProps = {
  name: 'Stranger'
};
```

###  API

####  定义组件

React.Component

React.PureComponent

####  React.Component

常用生命周期函数

挂载

constuuctor()

render()            class组件中唯一必须实现的方法

componentDidMount()

更新

render()

componentDidUpdate()

卸载

compomentWillUnMount()

####  ReactDom

api   

- [`render()`](https://react.docschina.org/docs/react-dom.html#render)
- [`hydrate()`](https://react.docschina.org/docs/react-dom.html#hydrate)
- [`unmountComponentAtNode()`](https://react.docschina.org/docs/react-dom.html#unmountcomponentatnode)
- [`findDOMNode()`](https://react.docschina.org/docs/react-dom.html#finddomnode)
- [`createPortal()`](https://react.docschina.org/docs/react-dom.html#createportal)

###  Hook

可以让你在不编写class组件的情况下使用state和其他的react特性

#####  useState

```react
 // 声明一个叫 “count” 的 state 变量。  
const [count, setCount] = useState(0);
```

0为初始值，setCount为一个函数，可以对count进行修改

#####  useEffect

相当于class组件的componentDidMount()和componentDidUpdate()

可以在组件中多次使用useEffect

通过这个Hook，可以告诉React在渲染之后执行某些操作

若useEffect返回一个函数，则当React在执行清除操作(组件卸载时)时会调用它

#####  其他Hook

useContext()

useRender()

跳过Effect来进行性能优化

通过在useEffect提供第二个参数，来告诉React仅在该参数更新时执行useEffect

```react
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

####  使用

**`useState` 方法的返回值是什么？** 返回值为：当前 state 以及更新 state 的函数。这就是我们写 `const [count, `

`setCount] = useState()` 的原因。这与 class 里面 `this.state.count` 和 `this.setState` 类似，

在函数中，可以直接使用state

##  Redux

###  安装

yarn add redux

###  作用

做状态管理的，即数据管理，也就是数据不交给组件管理，交给redux管理

###  核心

action

reducer

store

action就是一些对象数据,reducer用于处理action并返回新的state,store用于将action与reducer结合起来

###  使用

建立action文件  为一个函数  返回一个action对象,其中必须要有type属性,其他根据业务进行添加,导出此action

建立reducer文件  为一个函数,参数为state和action,通过switch语法根据action的type来返回相应的新的state,导出此reduce

建立store文件   通过redux的createStore函数,参数为自定义的reducer,创建一个store,并暴露此store,其中此store有几个api,组件注册监听  store.subscribe(),参数为一个回调函数;store.getStore()得到新的action;store.dispatch(),参数为一个action,代表发送此action,会触发注册监听的回调

##  Create-React-App

###  安装

```react
yarn create react-app my-app
```

###  运行

### `yarn start`



###  打包

npm build

安装serve包

使用serve build 启动打包项目

##  React-Redux

###  安装

先安装redux

yarn add react-redux

###  connect

react-redux的函数

connect(...)(component)

参数

第一个参数,mapStateToProps(state,ownProps)  接收数据的函数,要接受数据则实现此函数,参数state就是经过reducer处理后返回的state,可以直接return它,他最后会成为组件的props的一部分,即当它改变时组件就会重新渲染.----这里的state是交给redux管理的state，就是将这个state传递给了组件的props

第二个参数,mapDispatchToProps()  发送数据的函,要发送数据则实现此函数,此函数第一个参数为dispatch,为一个函数,即原store中的dispatch(), ---此函数返回一个对象,k为发送action的函数名,v为实际这个函数的调用,实际即调用原来store中的dispatch,通过它传递action----------通过此函数,可以将那个k传递给组件的props,然后在props中调用发送action

##  react-router

<switch>

​	<Route></Route>

</switch>







