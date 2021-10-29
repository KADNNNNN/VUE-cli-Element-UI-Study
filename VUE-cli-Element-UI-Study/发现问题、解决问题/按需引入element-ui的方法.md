[toc]

# 1. 按需引入

1. 借助 babel-plugin-component，我们可以只引入需要的组件，以达到减小项目体积的目的：

```undefined
npm install babel-plugin-component -D
```

# 2. 更改.babelrc文件

```csharp
    "plugins": [
    [
      "component",
      {
        "libraryName": "element-ui",
        "styleLibraryName": "theme-chalk"
      }
    ]
  ]
```

当然这里如果有其他的配置，只需要在 plugins 的数组中继续添加我们需要的配置代码就行了

# 3. 将按需引入的代码单独分割一下

1. 在 src 文件夹中新建我们的 element 文件夹，并在里面新建一个 index.js 文件

2. 在index文件中去书写我们需要引入的部分组件

```rust
// 导入自己需要的组件
import { Select, Option, OptionGroup, Input, Tree, Dialog, Row, Col } from 'element-ui'
const element = {
  install: function (Vue) {
    Vue.use(Select)
    Vue.use(Option)
    Vue.use(OptionGroup)
    Vue.use(Input)
    Vue.use(Tree)
    Vue.use(Dialog)
    Vue.use(Row)
    Vue.use(Col)
  }
}
export default element
```

这里要使用 Select 组件，必须同时使用 Option 和 OptionGroup
这里的 install 方法表示在 main.js 中，如果使用 Vue.use() 方法的话，则该方法默认会调用 install 方法

3. 在 main.js 中使用该文件，就大功告成了

```jsx
    // css样式还是需要全部引入
    import 'element-ui/lib/theme-chalk/index.css'
    import element from './element/index'
    Vue.use(element)
```

4. 为什么要使用 单独分割的方式去按需加载

   我们使用常规的方式再来加载一次，在 main.js 文件中 直接使用

```rust
import 'element-ui/lib/theme-chalk/index.css'
import { Dialog, Select, Option, OptionGroup, Input, Tree, Row, Col } from 'element-ui'
Vue.use(Select)
Vue.use(Option)
Vue.use(OptionGroup)
Vue.use(Input)
Vue.use(Tree)
Vue.use(Dialog)
Vue.use(Row)
Vue.use(Col)
```

会报错！

我们在 element-ui 的源码中可以看到，的确使用的是 Dialog，但是我们在运行的时候还是报错了

