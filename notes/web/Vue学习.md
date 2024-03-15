# 初识Vue

1. 想让Vue工作，就必须创建一个Vue实例，且要传入一个配置对象。
2. root容器里的代码依然符合html规范，但混入一些特殊的Vue语法
3. root容器里的代码被称为Vue模板
4. Vue实例和容器是一一对应的
5. 真实开发只有一个Vue实例，并且会配合组件一起使用。
6. {{xxxx}} 中的xxx要写js表达式， 且xxx可以自动读取到data里的所有属性
7. 一旦data里的数据发生改变，页面中用到该数据的地方会自动更新
8.  js表达式和js代码（语句）的区分
   1. 表达式：一个表达式会产生一个值，可以放在任何一个需要值的地方
      1. a
      2. a+b
      3. demo(1)
      4. x === y ？ 'a' : 'b'
   2. js代码（语句）
      1. if(){}
      2. for(){}

### 模板语法

1. 插值语法：
   - 功能：用于解析标签体内容
   - 写法：{{xxx}}，xxx是js表达式，且可以直接读取到data中的所有区域
2. 指令语法：
   - 功能：用于解析标签（包括：标签属性、标签体内容、绑定事件…）
   - 举例：<a v-bind:href="xxx">或简写为<a :href="xxx">，xxx同样要写js表达式，且可以直接读取到data中的所有属性

### 数据绑定

1. 单向绑定（v-bind）：数据只能从data流向页面

2. 双向绑定（v-model）：数据不仅能从data流向页面，还可以从页面流向data

   > 双向绑定一般都应用在表单类元素（可以输入的元素）上（如：<input>、<select>、<textarea>等）
   > v-model:value可以简写为v-model，因为v-model默认收集的就是value值

### el与data的两种写法

1. el

   ```Vue
   const vm = new Vue({
   	el:'#root', //第一种写法
   	data:{
   		name:'JOJO'
   	}
   })
   vm.$mount('#root')//第二种写法
   ```

2. data

   ```vue
   data:{
   	name:'JOJO'
   }
   ```

   ```vue
   data(){
   	return{
   		name:'JOJO'
   	}
   }
   ```

   
### 数据代理

![image-20220925215057541](C:\Users\69425\AppData\Roaming\Typora\typora-user-images\image-20220925215057541.png)

1. 通过vm对象来代理data对象中属性的操作（读/写）
2. 代理的好处是更加方便的操作data中的数据
3. 原理：通过Object.defineProperty()把data对象中的所有属性添加到vm上，为每一个添加到vm上的属性都指定一个getter/setter，在getter/setter的内部去操作读写data中对应的属性。



### 事件处理

1. 使用v-on:xxx或@xxx绑定事件，其中xxx是事件名
2. 事件的回调需要配置在methods对象中，最终会在vm上
3. methods中配置的函数，==不要用箭头函数！==否则this就不是vm了
4. methods中配置的函数，都是被Vue所管理的函数，this的指向是vm或组件实例对象
5. @click="demo和@click="demo($event)"效果一致，但后者可以传参

### 事件修饰符

1. prevent：阻止默认事件（常用）

2. stop：阻止事件冒泡（常用）

3. once：事件只触发一次（常用）

4. capture：使用事件的捕获模式

5. self：只有event.target是当前操作的元素时才触发事件

6. passive：事件的默认行为立即执行，无需等待事件回调执行完毕

   > 修饰符可以连续写，比如可以这么用：@click.prevent.stop="showInfo"

### 常用的按键别名

1. 回车：enter
2. 删除：delete (捕获“删除”和“退格”键)
3. 退出：esc
4. 空格：space
5. 换行：tab (特殊，必须配合keydown去使用)
6. 上：up
7. 下：down
8. 左：left
9. 右：right

> 注意：
>
> - 系统修饰键（用法特殊）：ctrl、alt、shift、meta
>
> - 配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发
>   配合keydown使用：正常触发事件
>   可以使用keyCode去指定具体的按键，比如：@keydown.13="showInfo"，但不推荐这样使用
> - Vue.config.keyCodes.自定义键名 = 键码，可以自定义按键别名





### 计算属性

定义：要用的属性不存在，需要通过已有属性计算得来。

原理：底层借助了Objcet.defineproperty()方法提供的getter和setter。

get函数什么时候执行？ 

- 初次读取时会执行一次
- 当依赖的数据发生改变时会被再次调用

优势：与methods实现相比，内部有缓存机制（复用），效率更高，调试方便

> 备注：
>
> 计算属性最终会出现在vm上，直接读取使用即可
> 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变
> 如果计算属性确定不考虑修改，可以使用计算属性的简写形式



### 监视属性watch

1. 当被监视的属性变化时，回调函数自动调用，进行相关操作
2. 监视的属性必须存在，才能进行监视
3. 监视有两种写法：
   1. 创建Vue时传入watch配置
   2. 通过`vm.$watch`监视



### 深度监视

1. Vue中的watch默认不监测对象内部值的改变（一层）
2. 在watch中配置deep:true可以监测对象内部值的改变（多层）

> 备注：
>
> Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以
> 使用watch时根据监视数据的具体结构，决定是否采用深度监视



### 监视属性简写

如果监视属性除了handler没有其他配置项的话，可以进行简写。



#### computed和watch之间的区别

1. computed能完成的功能，watch都可以完成
2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作

> 两个重要的小原则：
>
> - 所有被Vue管理的函数，最好写成普通函数，这样this的指向才是vm 或 组件实例对象
> - 所有不被Vue所管理的函数（定时器的回调函数、ajax的回调函数等、Promise的回调函数），最好写成箭头函数，这样this的指向才是vm 或 组件实例对象。



### 绑定样式

class样式：

写法：class="xxx"，xxx可以是字符串、对象、数组

字符串写法适用于：类名不确定，要动态获取

对象写法适用于：要绑定多个样式，个数不确定，名字也不确定

数组写法适用于：要绑定多个样式，个数确定，名字也确定，但不确定用不用

style样式：

```vue
:style="{fontSize: xxx}"  #其中xxx是动态值
:style="[a,b]" #其中a、b是样式对
```

### 条件渲染

1. v-if：

   - 写法：

   - v-if="表达式"
     v-else-if="表达式"
     v-else
     适用于：切换频率较低的场景
   - 特点：不展示的DOM元素直接被移除

   - 注意：v-if可以和v-else-if、v-else一起使用，但要求结构不能被打断

2. v-show：

   - 写法：v-show="表达式"
   - 适用于：切换频率较高的场景
   - 特点：不展示的DOM元素未被移除，仅仅是使用样式隐藏掉
     使用v-if的时，元素可能无法获取到，而使用v-show一定可以获取到

### 列表渲染

#### 基本列表

`v-for`指令：

1. 用于展示列表数据
2. 语法：`<li v-for="(item, index) in xxx" :key="yyy">`，其中key可以是index，也可以是遍历对象的唯一标识
3. 可遍历：数组、对象、字符串（用的少）、指定次数（用的少）

#### key的作用与原理

面试题：react、vue中的key有什么作用？（key的内部原理）

虚拟DOM中key的作用：key是虚拟DOM中对象的标识，当数据发生变化时，Vue会根据【新数据】生成【新的虚拟DOM】，随后Vue进行【新虚拟DOM】与【旧虚拟DOM】的差异比较，比较规则如下：

对比规则：

旧虚拟DOM中找到了与新虚拟DOM相同的key：

若虚拟DOM中内容没变, 直接使用之前的真实DOM
若虚拟DOM中内容变了, 则生成新的真实DOM，随后替换掉页面中之前的真实DOM
旧虚拟DOM中未找到与新虚拟DOM相同的key：创建新的真实DOM，随后渲染到到页面

用index作为key可能会引发的问题：

若对数据进行逆序添加、逆序删除等破坏顺序操作：会产生没有必要的真实DOM更新 ==> 界面效果没问题, 但效率低
若结构中还包含输入类的DOM：会产生错误DOM更新 ==> 界面有问题
开发中如何选择key?

最好使用每条数据的唯一标识作为key，比如id、手机号、身份证号、学号等唯一值
如果不存在对数据的逆序添加、逆序删除等破坏顺序的操作，仅用于渲染列表，使用index作为key是没有问题的



#### 列表过滤

#### 列表排序

#### 数据监视

1. Vue监视数据的原理：

   ​	vue会监视data中所有层次的数据

2. 如何监测对象中的数据？、

   ​	通过setter实现监视，且要在new Vue时就传入要监测的数据

   1. 对象中后追加的属性，Vue默认不做响应式处理

   2. 如需给后添加的属性做响应式，请使用如下API：

      - Vue.set(target,propertyName/index,value)

      - vm.$set(target,propertyName/index,value)

3. 如何监测数组中的数据？

4. 通过包裹数组更新元素的方法实现，本质就是做了两件事：

   1. 调用原生对应的方法对数组进行更新
   2. 重新解析模板，进而更新页面

5. 在Vue修改数组中的某个元素一定要用如下方法：

   1. 使用这些API：push()、pop()、shift()、unshift()、splice()、sort()、reverse()

   2. Vue.set() 或 vm.$set()

      > 特别注意：Vue.set() 和 vm.$set() 不能给vm 或 vm的根数据对象（data等） 添加属性



### 收集表单数据

收集表单数据：

- 若：<input type="text"/>，则v-model收集的是value值，用户输入的内容就是value值
- 若：<input type="radio"/>，则v-model收集的是value值，且要给标签配置value属性
- 若：<input type="checkbox"/>
  - 没有配置value属性，那么收集的是checked属性（勾选 or 未勾选，是布尔值）
  - 配置了value属性：
    - v-model的初始值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
    - v-model的初始值是数组，那么收集的就是value组成的数组

v-model的三个修饰符(v-model.xxx)：

1. lazy：失去焦点后再收集数据
2. number：输入字符串转为有效的数字
3. trim：输入首尾空格过滤

#### 过滤器：

- 定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）。

- 语法：

  1. 注册过滤器：Vue.filter(name,callback) 或 new Vue{filters:{}}

  2. 使用过滤器：{{ xxx | 过滤器名}} 或 v-bind:属性 = "xxx | 过滤器名"


> 备注：
>
> 过滤器可以接收额外参数，多个过滤器也可以串联
> 并没有改变原本的数据，而是产生新的对应的数据

### 内置指令

#### v-text指令

- 之前学过的指令：

  - v-bind：单向绑定解析表达式，可简写为:

  - v-model：双向数据绑定

  - v-for：遍历数组 / 对象 / 字符串

  - v-on：绑定事件监听，可简写为@

  - v-if：条件渲染（动态控制节点是否存存在）

  - v-else：条件渲染（动态控制节点是否存存在）

  - v-show：条件渲染 (动态控制节点是否展示)

- v-text指令：

  - 作用：向其所在的节点中渲染文本内容

  - 与插值语法的区别：v-text会替换掉节点中的内容，{{xx}}则不会。



#### v-html指令

1. 作用：向指定节点中渲染包含html结构的内容
2. 与插值语法的区别：
   - v-html会替换掉节点中所有的内容，{{xx}}则不会
   - v-html可以识别html结构
3. 严重注意：
   - v-html有安全性问题！！！
   - 在网站上动态渲染任意HTML是非常危险的，容易导致XSS攻击
   - 一定要在可信的内容上使用v-html，永远不要用在用户提交的内容上！！！



#### v-cloak指令

`v-cloak`指令（没有值）：

1. 本质是一个特殊属性，Vue实例创建完毕并接管容器后，会删掉`v-cloak`属性
2. 使用css配合`v-cloak`可以解决网速慢时页面展示出`{{xxx}}`的问题

#### v-once指令

`v-once`指令：

1. `v-once`所在节点在初次动态渲染后，就视为静态内容了
2. 以后数据的改变不会引起`v-once`所在结构的更新，可以用于优化性能

#### v-pre指令

`v-pre`指令：

1. 跳过其所在节点的编译过程。
2. 可利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译

#### 自定义指令

总结：

自定义指令定义语法：

局部指令：

```Vue
new Vue({
	directives:{指令名:配置对象}   
}) 	
new Vue({
	directives:{指令名:回调函数}   
})
```


全局指令：

```Vue
Vue.directive(指令名,配置对象)
Vue.directive(指令名,回调函数)
```

配置对象中常用的3个回调函数：

bind(element,binding) ：指令与元素成功绑定时调用
inserted(element,binding)：指令所在元素被插入页面时调用
update(element,binding)：指令所在模板结构被重新解析时调用
备注：

指令定义时不加“v-”，但使用时要加“v-”

指令名如果是多个单词，要使用kebab-case命名方式，不要用camelCase命名

### Vue生命周期

1. 生命周期：
   1. 又名：生命周期回调函数、生命周期函数、生命周期钩子
   2. 是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数
   3. 生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的
   4. 生命周期函数中的this指向是vm 或 组件实例对象
2. 常用的生命周期钩子：
   1. mounted：发送ajax请求、启动定时器、绑定自定义事件、订阅消息等初始化操作（如页面标签挂载后，启动定时器）
   2. beforeDestroy：清除定时器、解绑自定义事件、取消订阅消息等收尾工作
3. 销毁Vue实例：
   1. 销毁后借助Vue开发者工具看不到任何信息
   2. 销毁后自定义事件会失效，但原生DOM事件依然有效
   3. 一般不会在beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了





# Vue组件化编程

### 模块与组件、模块化与组件化

- 模块

  - 理解：向外提供特定功能的 js 程序，一般就是一个 js 文件	
  - 为什么：js 文件很多很复杂
  - 作用：复用 js，简化 js 的编写，提高 js 运行效率
- 组件
  - 定义：用来实现局部功能的代码和资源的集合（html/css/js/image…）
  - 为什么：一个界面的功能很复杂
  - 作用：复用编码，简化项目编码，提高运行效率
- 模块化
  - 当应用中的 js 都以模块来编写的，那这个应用就是一个模块化的应用

- 组件化
  - 当应用中的功能都是多组件的方式来编写的，那这个应用就是一个组件化的应用



### Vue中使用组件的三大步骤

1. 定义组件(创建组件)

2. 注册组件
3. 使用组件(写组件标签)

- 如何定义一个组件？
  - 使用Vue.extend(options)创建，其中options和new Vue(options)时传入的options几乎一样，但也有点区别：

- el不要写，为什么？

  - 最终所有的组件都要经过一个vm的管理，由vm中的el决定服务哪个容器

- data必须写成函数，为什么？

  - 避免组件被复用时，数据存在引用关系

- 如何注册组件？

  - 局部注册：new Vue的时候传入components选项
  - 全局注册：Vue.component('组件名',组件)
-  编写组件标签：<school></school>



### 关于组件名

**一个单词组成**

第一种写法（首字母小写）：school

第二种写法（首字母大写）：School

**多个单词组成**

第一种写法（kebab-case命名）：my-school
第二种写法（CamelCase命名）：MySchool （需要Vue脚手架支持）

> 备注：组件名尽可能回避HTML中已有的元素名称，
>
> 例如：h2、H2都不行
>
> options中可以使用name配置项指定组件在开发者工具中呈现的名字

### 关于组件标签

第一种写法：<school></school>
第二种写法：<school/>

> 备注：不使用脚手架时，<school/>会导致后续组件不能渲染
> 一个简写方式：const school = Vue.extend(options)可简写为：const school = options





### 关于VueComponent

school组件本质是一个名为VueComponent的构造函数，且不是程序员定义的，是Vue.extend生成的

我们只需要写<school/>或<school></school>，Vue解析时会帮我们创建school组件的实例对象，即Vue帮我们执行的：new VueComponent(options)

特别注意：每次调用Vue.extend，返回的都是一个全新的VueComponent！

**关于this指向**

- 组件配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是VueComponent实例对象new
- Vue(options)配置中：data函数、methods中的函数、watch中的函数、computed中的函数 它们的this均是Vue实例对象

Vue的实例对象，以后简称vm。

> VueComponent的实例对象，以后简称vc（也可称之为：组件实例对象）
>
> 只有在本笔记中VueComponent的实例对象才简称为vc



### 一个重要的内置关系

1. 一个重要的内置关系：`VueComponent.prototype.__proto__ === Vue.prototype`

   > VueComponent（构造函数）的prototype（即VueComponent的原型对象）的_ _proto _ _(即Vue的原型对象) === Vue（构造函数）的 prototype（Vue的原型对象）

2. 为什么要有这个关系：让组件实例对象（vc）可以访问到 Vue 原型上的属性、方法

   > 访问实例对象vc的数据时，找不到会到vc的_ _proto _ _里面找，即找到Vue

# Vue CLI

1. Vue 脚手架是 Vue 官方提供的标准化开发工具（开发平台）

2. 步骤

   - 如果下载缓慢请配置 npm 淘宝镜像：npm config set registry http://registry.npm.taobao.org

   - 全局安装@vue/cli：npm install -g @vue/cli

   - 切换到你要创建项目的目录，然后使用命令创建项目：vue create xxxx
   - 选择使用vue的版本

   - 启动项目：npm run serve

   - 暂停项目：Ctrl+C

   > Vue 脚手架隐藏了所有 webpack 相关的配置，若想查看具体的 webpakc 配置，请执行：
   >
   > ```
   > vue inspect > output.js
   > ```

3. 脚手架文件结构

   ```txt
   .文件目录
   ├── node_modules 
   ├── public
   │   ├── favicon.ico: 页签图标
   │   └── index.html: 主页面
   ├── src
   │   ├── assets: 存放静态资源
   │   │   └── logo.png
   │   │── component: 存放组件
   │   │   └── HelloWorld.vue
   │   │── App.vue: 汇总所有组件
   │   └── main.js: 入口文件
   ├── .gitignore: git版本管制忽略的配置
   ├── babel.config.js: babel的配置文件
   ├── package.json: 应用包配置文件 
   ├── README.md: 应用描述文件
   └── package-lock.json: 包版本控制文件
   ```

   

### Vue的不同版本js

**vue.js 与 vue.runtime.xxx.js的区别**

- vue.js 是完整版的 Vue，包含：核心功能+模板解析器

- vue.runtime.xxx.js 是运行版的 Vue，只包含核心功能，没有模板解析器。

  > 因为 vue.runtime.xxx.js 没有模板解析器，所以不能使用 template 配置项，
  >
  > 需要使用 render函数接收到的createElement 函数去指定具体内容

