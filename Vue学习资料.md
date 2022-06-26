# Vue笔记
## Vue核心
### 什么是Vue
 >Vue是一套用于**构建用户界面**的**渐进式**JavaScript框架  
 >渐进式：Vue可以自底向上逐层应用；简单应用：只需一个轻量化的核心库；复杂应用：可以引入各式各样的Vue插件  
### 谁开发的  
 >尤雨溪
### Vue的特点
 1. 采用**组件化**模式，提高代码复用率、且让代码更好维护。
 2. **声明式**编码，让编码人员无需直接操作DOM，提高开发效率。

## Vue编码上手
### 初识Vue
1. 想让Vue工作，就必须创建一个Vue实例，且要传入一个配置对象；  
```html
<div id="root">
<h1>hello,{{name}}</h1>
</div>
```
```javascript
Vue.config.productionTip = false//阻止vue在启动时生成的生产提示
	new Vue({
		el:'#app',//用于指定当前vue实例为哪个容器服务,值通常为css的选择器。
		//也可以用 vue对象.$mount('#root') 指定容器，
		data:{//data用于存储数据，供el指定的容器使用。暂时为对象
			name:'world'
		}
	})
```
2. root容器里的代码依然符合html规范，只不过混入了一些特殊的Vue语法；
3. root容器里的代码被称为【Vue模板】；
4. Vue实例与容器是一一对应的；
5. 真实的开发中只有一个Vue实例，并且会配合着组件一起使用；
6. {{XXX}}中的XXX要写计算表达式，且XXX可以自动读取到data中的所有属性；
7. 一旦data中的数据发生改变，那么页面中用到该数据的地方也会自动更新；

### Vue模板语法
1. 插值语法：
>功能：用于解析标签体内容。  
>写法：{{XXX}}，XXX是js表达式，且可以直接读取到data中的所有属性。
2. 指令语法：
>功能：用于解析标签（包括：标签属性、标签体内容、绑定事件...）。  
>举例：v-bind:href="xxx" 或简写为 :href="xxx"，xxx同样要写js表达式，且可以直接读取到data中的所有属性。  
>备注：Vue中有很多的指令，且形式都为 v-???? ，此处只是用 v-bind 举例。

### 数据绑定
Vue中有2种数据绑定的方式：
1. 单向绑定(v-bind)：数据只能从data流向页面。
2. 双向绑定(v-model)：数据不仅能从data流向页面，还可以从页面流向data。
>备注：
> 1. 双向绑定一般都应用在表单类元素上（如：input、select等）  
> 2. v-model:value  可以简写为 v-model，因为v-model默认收集的就是value值。

### el与data的两种写法
data与el的两种写法
1. el的两种写法
	1. new Vue时配置el属性
	2. 先创建Vue实例，随后再通过 Vue实例.$mount('#root')指定el值。
2. data的两种写法
	1. 对象式
```javascript
	data{
		key:value,
		...
	}
```
	2. 函数式
```javascript
	data:function(){
		return{
			key:value
		}
	}
```
	如何选择：两者皆可，但用到组件时，data必须使用函数式，否则会报错。
3. 一个重要原则：
	由Vue管理的函数，一定不要写成箭头函数，一旦写了，this就不再是Vue实例了。

### MVVM模型
1. M：模型(Model)：对应data中的数据
2. V：视图(View)：模板、DOM、页面
3. VM：视图模型(ViewModel)：Vue实例对象
观察得知：
1. data中的所有属性，都会出现在Vue实例身上
2. 实例身上的所有属性及Vue原形上所有属性，在Vue模板中都可以直接使用

### 数据代理
定义：通过一个对象代理另一个对象中属性的操作(读/写)。
方法：Object.defineProperty(obj(对象),value(代理的属性),function(){}(方法))

### 事件处理
#### 事件的基本使用：
1. 使用v-on:xxx 或 @xxx 绑定事件，其中xxx是需要绑定的事件名称；
2. 事件的回调需要配置在methods对象中，最终会在vm上；
3. methods中配置的函数，不要使用箭头函数！否则this就不是vm了；
4. methods中配置的函数，都是被Vue所管理的函数，this的指向是vm或组件实例对象；
5. @click="demo" 和 @click="demo($event)" 效果一致，但后者可以传参；

#### 事件修饰符
Vue中的事件修饰符：
1. prevent:阻止默认事件(常用)；
2. stop:阻止事件冒泡(常用)；
3. once:事件只会触发一次(常用)；
4. capture:使用事件的捕获模式；
5. self:只有event.target是当前操作的元素时才触发事件；
6. passive:事件的默认行为立即执行，无需等待事件回调执行完毕；

#### 键盘事件
1. Vue中常用的按键别名
	> 回车 => enter  
	> 删除 => delete  
	> 退出 => esc  
	> 空格 => space  
	> 换行 => tab  
	> 上 => up  
	> 下 => down  
	> 左 => left  
	> 右 => right  
2. Vue未提供别名的按键，可以使用按键原始的key值去绑定，但注意要转为kebab-case(短横线命名)。
3. 系统修饰键(用法特殊)：Ctrl、Alt、Shift、Meta
	1. 配合keyup使用：按下修饰键的同时，再按下其他键，随后释放其他键，事件才被触发。
	2. 配合keydown使用：正常触发事件。
4. 也可以使用keyCode去指定具体的按键（不推荐）
5. Vue.config.keyCode.自定义键名 = 键码 ,可以去定制按键别名

### 计算属性 computed
1. 写法：
	1. 全写：
```javascript
computed:{
	property:{//property是自定义的计算属性
		get(){//当有人读取属性时，get就会被调用，返回值就作为属性的值
			//执行操作...
			return value(作为属性的值)
		}
		set(){//如果没有设置计算属性的需要可不写set方法
			...
		}
	}
}
```
	2. 简写：
```javascript
property{
	//property是自定义的计算属性
	//方法默认为get()
	//方法体...
}
```
2. 定义：要用的属性不存在，需要通过已有的属性计算得来。
3. 原理：底层借助了Object.defineproperty方法提供的getter和setter。
4. get函数什么时候执行？
	1. 初始读取会执行一次
	2. 当依赖的数据发生改变时会被再次调用。
4. 优势：与methods实现相比，内部有缓存机制(复用)，效率更高，调试方便
5. 备注：
	1. 计算属性最终会出现在vm上，直接读取使用即可。
	2. 如果计算属性要被修改，那必须写set函数去响应修改，且set中要引起计算时依赖的数据发生改变

### 监视属性 watch
1. 监视的两种写法
	1. new Vue时传入watch配置
```javascript
	watch:{
		property:{
			//property是被监视的属性
			immediate:true//该方法立即执行一次
			handler(newValue,oldValue){//当被监视属性发生改变时，该方法就会被调用。
			//方法体
			}
		}
	}
```
	2. 通过vm.$watch监视
```javascript
	vm.$watch('property',{
		//property是被监视的属性
		immediate:true//该方法立即执行一次
		handler(newValue,oldValue){//当被监视属性发生改变时，该方法就会被调用。
		//方法体
		}
	})
```

2. 当被监视的属性变化时，回调函数自动调用，进行相关操作
3. 监视的属性必须存在，才能进行监视！！
4. 当watch内部只使用到handler方法时，可简写为：
```javascript
	watch:{
		property:(newValue,oldValue){//当被监视属性发生改变时，该方法就会被调用。
			//property是被监视的属性
			//方法体
		}
	}
```
或者
```javascript
	vm.$watch('property',function(newValue,oldValue){
		//property是被监视的属性
		//当被监视属性发生改变时，该方法就会被调用。
		//方法体
	})
```

#### 深度监视
深度监视：
1. Vue中的watch默认不监视对象内部值的改变（一层）。
2. 配置deep:true可以监视到对象内部值的改变（多层）。
备注：
1. Vue自身可以监测对象内部值的改变，但Vue提供的watch默认不可以；
2. 使用watch时根据数据的具体结构，决定是否使用深度监视

### 计算属性computed与监视属性watch对比
computed与watch间的区别：
1. computed能完成的功能，watch都可以完成
2. watch能完成的功能，computed不一定能完成，例如：watch可以进行异步操作。
两个重要的小原则：
1. 所被Vue管理的函数，最好写成普通函数，这样this的指向才是**vm**或**组件实例对象**。
2. 所有不被Vue所管理的函数(例如定时器的回调函数、ajax的回调函数、Promise的回调函数等)，最好写成箭头函数。这样this的指向才是**vm**或**组件实例对象**。

### class与style绑定
#### class绑定
1. 绑定class样式--字符串写法，适用于：样式的类名不确定，需要动态指定
```html
<div class="basic" :class="classStr"></div>
```
```javascript
new Vue({
	el:'#root',
	data:{
		classStr:'test1',
	}
})
```
2. 绑定class样式--数组写法，适用于：要绑定的样式的个数不确定，名字也不确定
```html
<div class="basic" :class="classArr"></div>
```
```javascript
new Vue({
	el:'#root',
	data:{
		classArr:['test1','test2','test3'],
	}
})
```
3. 绑定class样式--对象写法，适用于：要绑定的样式个数确定，名字确定，但需要动态决定是否使用
```html
<div class="basic" :class="classObj"></div>
```
```javascript
new Vue({
	el:'#root',
	data:{
		classObj:{
			test1:false,
			test2:true,
			test3:false,
		},
	}
})
```
或者直接写在标签内
```html
<div class="basic" :class="{test1:a,test2:b,test3:c}"></div>
```
```javascript
new Vue({
	el:'#root',
	data:{
		a:true,
		b:false,
		c:true,
	}
})
```

#### style绑定
1. 绑定style样式--对象写法
```html
<div class="basic" :style="styleObj"></div>
```
```javascript
new Vue({
	el:'#root',
	data:{
		styleObj:{
			fontSize: '40px',
			color: 'red',
		}
	}
})
```
2. 绑定style样式--数组写法
```html
<div class="basic" :style="[styleObj1,styleObj2]"></div>
```
或者
```html
<div class="basic" :style="styleArr"></div>
```
```javascript
new Vue({
	el:'#root',
	data:{
		styleArr:[
			{
				fontSize: '40px',
			},
			{	
				color: 'red',
			}
		]
	}
})
```

### 条件渲染
1. v-if 
	> 写法：  
	> (1). v-if="表达式"  
	> (2). v-else-if="表达式"  
	> (3). v-else="表达式"  
	> 适用于：切换频率较低的场景。  
	> 特点：不展示的DOM元素直接被移除。  
	> 注意：v-if 可以和 v-else-if、v-else一起使用，但要求结构不能被“打断”  
2. v-show
	> 写法：v-show="表达式"  
	> 适用于：切换频率较高的场景。  
	> 特点：不展示的DOM元素直接未被移除，仅仅是使用样式隐藏了。  
3. 备注：使用v-if时，由于DOM元素可能被移除，导致无法获取到，而v-show一定可以获取到。

### 列表渲染
v-for指令
1. 用于展示列表数据
2. 语法：v-for="(item,index) in xxx" :key="yyy"
3. 可遍历：数组、对象、字符串（很少用）、指定次数（很少用）
4. 用法：
	1. 数组遍历
```html
<ul>
<!--遍历数组-->
	<li v-for="p in persons" :key="p.id">
	{{p.name}}-{{p.age}}
	</li>
</ul>
```
```javascript
new Vue({
	el:'#root',
	data:{
		persons:[
			{id:'01',name:'text1',age:18},
			{id:'02',name:'text2',age:18},
			{id:'03',name:'text3',age:18}
		]
	}
})
```
	2. 对象遍历
```html
<ul>
<!--对象数组-->
	<li v-for="(value,key) in persons" :key="key">
	{{key}}-{{value}}
	</li>
</ul>
```
```javascript
new Vue({
	el:'#root',
	data:{
		persons:{
			name:'text',
			age:18
		}
	}
})
```
	3. 字符串遍历
```html
<ul>
<!--对象数组-->
	<li v-for="(char,index) in str" :key="index">
	{{index}}-{{char}}
	</li>
</ul>
```
```javascript
new Vue({
	el:'#root',
	data:{
		str:'text'
	}
})
```
4. **注意**：react、vue中的key有什么用(key的内部原理)
	1. 虚拟DOM中key的作用
	> key是虚拟DOM对象的标识，当数据发生变化时，Vue会根据**新数据**生成**新的虚拟DOM**，  
	> 随后Vue进行**新虚拟DOM**与**旧虚拟DOM**的差异比较。
	2. 比较规则:
		1. 旧虚拟DOM中找到了与新虚拟DOM相同的key
			1. 若虚拟DOM中的**内容没变**，直接使用之前的真是DOM！
			2. 若虚拟DOM中内容变了，则生成新的真实DOM，随后替换掉页面中之前的真实DOM
		2. 旧虚拟DOM中未找到与新虚拟DOM相同的key
			1. 创建新的真实DOM，随后渲染到页面
	3. 直接用index作为key可能引发的问题：
		1. 若对数据进行逆序添加、逆序删除等破坏原有顺序的操作，会产生没有必要的真实DOM更新，效率变低
		2. 如果结构中还包含输入类的DOM，会产生错误的DOM更新，界面出现问题。
	4. 开发中如何选取key？
		1. 最好使用每条数据的唯一标识作为key，比如id、手机号、身份证号、学号等唯一值。
		2. 如果不存在对数据的逆序添加、逆序删除等破坏原有顺序的操作，仅用于渲染列表用于展示，可以使用index作为key。

### Vue监视数据原理
1. vue会监视data中所有层次的数据
2. 如何监视对象中的数据？
	> 通过setter实现监视，且要在new Vue 时就传入要监视的数据  
	> (1). 对象中后追加的属性，Vue默认不做响应式处理  
	> (2). 如需给后添加的属性做响应式，请使用如下API：  
	> Vue.set(target,propertyName/index,value) 或 vm.$set(target,propertyName/index,value)
3. 如何监视数组中的数据？
	> 通过包裹数组更新元素的方法实现，本质就是做了两件事：  
	> (1). 调用原生对应的方法对数组进行更新  
	> (2).重新解析模板，进而更新页面  
4. 在Vue修改数组中的某个元素一定要用如下方法：
	1. 使用这些API：push()、pop()、shift()、unshift()、splice()、sort()、reverse()
	2. Vue.set() 或 vm.$set()
**特别注意**：Vue.set() 和 vm.$set() 不能给vm 或 vm的根数据对象 添加属性！！！

### 收集表单数据：
若：`<input type="text"/>`，则v-model收集的是value值，用户输入的就是value的值；  
若：`<input type="radio"/>`，则v-model收集的是value值，且要给标签配置value值；  
若：`<input type="checkbox"/>`
1. 没有配置input的value属性，那么收集的就是checked（勾选 or 未勾选，是布尔值）
2. 若配置过input的value属性：
	1. 若v-model的初识值是非数组，那么收集的就是checked（勾选 or 未勾选，是布尔值）
	2. 若v-model的初识值是数组，那么收集的就是value组成的数组
备注：v-model的三个修饰符：
> lazy:失去焦点后再收集数据  
> number: 输入字符串转为有效的数字  
> trim: 输入首尾空格过滤  

### 过滤器
定义：对要显示的数据进行特定格式化后再显示（适用于一些简单逻辑的处理）。
语法：
1. 注册过滤器：`Vue.filter(name,callback)` 或 `new Vue{filters:{}}`
2. 使用过滤器：{{xxx|过滤器名称}} 或 v-bind:属性 = "xxx|过滤器名"
备注：
1. 过滤器也可以接受额外参数，多个过滤器也可以串联
2. 过滤器并没有改变原本的数据，是产生新的对应的数据

### Vue的其他指令
1. v-text指令
- 作用：向其所在的节点中渲染文本内容。
- 与插值语法的区别：v-text会替换掉节点中的内容，{{xxx}}插值则不会。
2. v-html指令
- 作用：向指定节点中渲染包含HTML结构的内容。
- 与插值语法的区别：
	1. v-text会替换掉节点中的内容，{{xxx}}插值则不会。
	2. v-html可以识别HTML结构。
- **严重注意**：v-html有安全性问题！！！
	1. 在网页上动态渲染任意HTML是非常危险的，容易导致XSS攻击
	2. 一定要在可信的内容上使用v-html，永远不要用在用户提交的内容上！
3. v-cloak指令（没有值）
- 本质上是一个特殊属性，Vue实例创建完毕并接管容器后，会删除v-cloak属性
- 可以使用css配合v-cloak可以解决网速慢时页面展示{{xxx}}的问题
4. v-once指令
- v-once所在的节点在初次动态渲染后，就视为静态内容了。
- 以后数据的改变不会引起v-once所在结构的更新，可以用来优化性能
5. v-pre指令
- 让Vue跳过其所在节点的编程过程。
- 可以利用它跳过：没有使用指令语法、没有使用插值语法的节点，会加快编译。

### 自定义指令
- 定义语法
	1. 局部指令：new Vue({directives:{指令名，配置对象}})或new Vue({directives:{指令名，回调函数}})
	2. 全局指令：Vue.directive(指令名，配置对象)或Vue.directive(指令名，回调函数)
- 配置对象中常用的3个回调：
	1. bind: 指令与元素成功绑定是被调用。
	2. inserted: 指令所在元素被插入页面时被调用。
	3. update: 指令所在模板结构被重新解析时调用。、
- 备注：
	1. 指令在定义时不用加v-，但使用时要加v-；
	2. 指令名如果是多个单词，要用kebab-case(短横线)命名方式，不要用camelCase(小驼峰)命名

### 生命周期

![生命周期图解](https://cn.vuejs.org/images/lifecycle.png)
解释：
- Init Events & Lifecycle ==> 初始化：生命周期、事件，但数据代理还未开始。
- beforeCreate ==> 生命周期函数:vm实例创建前。此时，无法通过vm访问到data中的数据、methods中的方法等。
- Init injections & reactivity ==> 初始化：数据监测、数据代理。
- created ==> 生命周期函数:vm实例创建完成时。此时，可以通过vm访问到data中的数据、methods中配置的方法等。
- Has "el" option 到 beforeMount前 ==> Vue开始解析模板，生成虚拟DOM(内存中)，页面还不能显示解析好的内容。
- beforeMount ==> 生命周期函数:vm实例挂载前。此时页面呈现的是未经过Vue编译的DOM结构，所有对DOM的操作，最终都不奏效。
- Create vm.$el and reolace "el" with it ==> 将内存中的虚拟DOM转为真实DOM插入页面
- mounted ==> 生命周期函数:vm实例挂载完成时。此时，页面中呈现的是经过Vue编译的DOM；对DOM的操作均有效(尽可能避免)。至此初始化过程结束，一般在此进行：开启定时器、发送网络请求、订阅消息、绑定自定义事件等初始化。
- beforeUpdate ==> 生命周期函数:数据更新前。此时，数据是新的，但页面是旧的，即：页面尚未和数据保持同步。
- Virtual DOM re-render and patch ==> 根据新的数据，生成新的虚拟DOM进行比较，最终完成页面更新，即：完成了Model ==> View的更新。
- updated ==> 生命周期函数:数据更新时。此时，数据和页面都已更新，即：页面和数据保持同步。
- beforeDestroy ==> 生命周期函数:vm销毁前。此时，vm中的所有的data、methods、指令等等都处于可用状态，马上执行销毁过程。一般在此阶段：关闭定时器、取消订阅消息，解绑自定义事件等收尾操作。

## Vue组件化编程
### 了解模块、组件，模块化与组件化
- 模块 
	1. 理解：向外提供特定功能的js程序，一般就是一个js文件。
	2. 为什么：js文件很多很复杂。
	3. 作用：复用js，简化js的编写，提高js的运行效率。
- 组件
	1. 理解：用来实现局部(特定)功能效果的代码集合(html/css/js/image......)。
	2. 为什么：一个界面的功能很复杂。
	3. 作用：复用编码，简化项目编码，提高运行效率。
- 模块化  
	当应用中的js都以模块来编写的，那这个应用就是一个模块化的应用。
- 组件化  
	当应用中的功能都是多组件的方式来编写的，那这个应用就是一个组件化的应用。