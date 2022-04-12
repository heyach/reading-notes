### 深入浅出React和Redux
***

### 组件开发
组件，指的是能完成某个特定功能的`独立的`，`可重用的`的代码。
基于组件的开发模式，可以把一个大的应用分解成若干小的组件，每个组件只关注某个特定功能，通过组合的方式，最终构建成一个功能庞大的应用，在此过程中，一些设计巧妙的组件，还能在不同的功能模块中复用

### 单文件组件SFC（single file component）
在传统的开发模式中，即是是同一个功能，也会将html，css，js强行分开放到3种不同的文件里，这样只是将不同的技术分开了，而不是将逻辑点分开，SFC强调将同一个逻辑的代码放到一起。
vue中关于关注点分离的看法
一个重要的事情值得注意，关注点分离不等于文件类型分离。在现代 UI 开发中，我们已经发现相比于把代码库分离成三个大的层次并将其相互交织起来，把它们划分为松散耦合的组件再将其组合起来更合理一些。在一个组件里，其模板、逻辑和样式是内部耦合的，并且把他们搭配在一起实际上使得组件更加内聚且更可维护

### 纯函数
纯函数指的是完全没有任何副作用（比如依赖外部变量，改变了外部变量等），输出完全依赖于输入的函数，只要传递相同的输入，一定会得到相同的输出。react的render就是一个纯函数，只要传递相同的data数据，就能得到一样的ui。`UI = render(data)`

### 虚拟DOM（Virtual DOM）
DOM是描述元素的结构化表达形式，浏览器根据DOM的逐级包含关系，构建一个DOM用来描述渲染界面。而VDOM是对DOM抽象，只会关注不触及浏览器的部分，而通过新旧VDOM的对比，可以得到最少量更新真是DOM的内容

### 组件构建原则
高内聚，低耦合
如果两个组件逻辑太紧密，无法清晰定义职责，那也许就不应该拆分

### 组件props的校验和初始值
在组件中声明了class后，可以通过propTypes和defaultProps来声明props的类型、必填项、初始值
```js
class Counter extends Component { }
Counter.propTypes = {
  caption: PropTypes.string.isRequired,
  initValue: PropTypes.number
};

Counter.defaultProps = {
  initValue: 0
};
```

### render
render是一个纯函数，所以不要在里面调用setState，也不往DOM树上渲染内容，只会返回一个描述要渲染的JSX

### 箭头函数绑定this
在jsx的元素上绑定的事件函数的this并不是当前class的this，而是浏览器的dom元素，所以要在constructor里绑定this
```js
constructor(props) {
  super(props);
  this.buttonCLick = this.buttonCLick.bind(this);
  this.buttonClick2 = this.buttonCLick2.bind(this);
}
```
而由于es6的箭头函数的this就是当前代码环境的this，所以可以通过箭头函数绑定this
```js
<button onClick={() => this.buttonClick()}
```
这样就可以调用到当前class的的函数了，但是这样每次render的时候都会创建一个匿名函数，其实并不提倡。


### jsx的优缺点 p17
### eject p19
### props和state p27
### 组件声明周期 p35
### flux p50
### redux 66
要更新页面渲染，就要更新应用状态，但是不是直接去改值，而是根据逻辑返回一个新的值给redux，由redux完成新的组装
`reducer(state, action)`根据state（上一次的结果）和这一次的元素状态产生一个新的状态
reducer是纯函数，结果只能由state和action决定，而且还不能修改state和action

### 无状态组件 p75
### 纯函数组件 p76

### provider p78
### react-redux connect p81
### react代码组织规范 p90 
按功能组织模块很有意思，就好像vue中如果我们要加一个功能模块
我们要在views里增加一个文件夹，在router里增加一个，在stores里也增加一个，如果像mango里还有什么api，config，那在对应的地方都要增加，这样当然可以完成功能
但是在模块之间相互依赖的时候，如果A模块依赖了B模块的内容，那么可能要导入好几个文件，比如api和config，但是如果按照功能组织，可以把对外接口放到index里统一暴露，也就是说，一个模块内部包含了全部的内容，对外也有统一的接口
```js
// 按角色组织
/config
  /a
  /b
/api
  /a
  /b
/views
  /a
  /b
/components
  /a
  /b
// b模块依赖a的内容
import aconfig from "../config/a"
import aapi from "../api/a"
import acomponent from "../api/a"
```
如果a模块有修改，那么怎么确保不影响其他的模块呢，比如api文件夹不叫api了，改成interface，b模块也要改，但是实际a模块并没有改对外接口
```js
// 按功能组织
a/
  /api
  /config
  /views
  /components
  index
    import api from "./api"
    import config from "./config"
    import components from "./components"
    export api.x1
    export config.x2
    export components.x3
b/
  /api
  /config
  /views
  /components
  index
// 在每个模块的index里完成对外接口的暴露，那么在别的模块要引入的时候，只需要引入对外接口，模块内部的修改只要不影响对外接口就可以了
import { x1, x2, x3 } from "./a/index"
```

### 状态树设计 92
### combineReducer 98
### 性能优化 115
### 性能优化之shouldComponentUpdate 120
### diff 127
### 高级组件 139
一个函数，接受一个组件作为参数，返回一个新的增强功能的组件
有称这个函数为高阶组件的，也有称这个新组件为高阶组件的，这个函数称为高阶组件工厂函数
```js
import React from "react"
function removeUserProp(WrappedComponent) {
  return class WrappingComponent extends React.Component {
    render() {
      const {
        user,
        ...otherProps
      } = this.props
      return <WrappedComponent {otherProps}>
    }
  }
}
```
新的组件跟原来组件功能基本一致，就是去掉了user这个prop
```js
const newCom = removeUserProp(oldCom)
// 这样就相当于定义了一个新的组件
```

### react的slot
组件包含的内容作为组件的props.children使用
```js
<CountDown>
  {
    (count) => <div>{count}</div>
  }
</CountDown>
```

### 与服务器通信 157
### 单元测试 190
### 同构，服务端渲染 245