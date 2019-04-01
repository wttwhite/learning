# 文件
  * 文件夹及文件名使用中划线连接(kebab-case)和小写 统一规范
    * 使用中划线方式命名可以避免出现大小写不敏感的文件系统问题
  * vue文件末尾留一空行
# JS代码规范
## 变量命名和赋值
  * 不要定义未使用的变量
  * 对于变量和函数名统一使用小驼峰命名法, 不用下划线
  - 全局变量为大写
  - 常量使用大写字母和下划线来组合命名，下划线用以分割单词
  * 构造函数要以大写字母开头
  * 不要省去小数点前面的0
  ```
  const discount = .5      // ✗ avoid
  const discount = 0.5     // ✓ ok
  ```
## 空格
  * 运算符前后添加空格(除单目运算符和 += -= ...这种)
    * a = a + 1
    * 字符串拼接 ( a = 'hello' + 'world' )
  * 逗号,分号后面加空格
    * a = [1, 2, 3, 4]
    * a (name, age) {}
    * for (let i = 0; i < items.length; i++) {...}
  * 键值对当中冒号与值之间要留空格
    * var obj = { 'key': 'value' }
  * 代码两个空格缩进(eslint: indent)
  * 关键字后面加空格
  ```
    if (condition) { ... }   // ✓ ok
    if(condition) { ... }    // ✗ avoid
  ```
  * 函数声明时括号与函数名间加空格
    * 声明空格: function () {}
    * 调用无空格: a()
  * 对于三元运算符 ? 和 : 与他们所负责的代码处于同一行
  ```
    1. 一行上
    let a = b === '1' ? 'ok' : 'no'
    2. 不同行
    let a = b === '1'
      ? 'ok' 
      : 'no'
  ```
  * 模板字符串中变量前后不加空格
  ```
  const message = `Hello, ${ name }`    // ✗ avoid
  const message = `Hello, ${name}`      // ✓ ok
  ```
  * 注释首尾留空格
  ```
  //comment           // ✗ avoid
  // comment          // ✓ ok

  /*comment*/         // ✗ avoid
  /* comment */       // ✓ ok
  ```
  - 为了便于阅读每行字符建议小于数 80 个
    - 如果一个 JS 语句超过了 80 个字符，建议在 运算符或者逗号后换行
## 注释
- 修复缺陷注释, 给出理由(简单的缺陷就不用了, 自己把控):
  ```
  .pagelayout-reset-padding0 {
    .layout-main {
      // 覆盖原有样式, img组件自带16px距离, 用important避免框架样式层级改变而不生效
      padding: 0 !important;
    }
  }
  ```
# 符号
  * 始终使用 === 替代 ==
  * 点号操作符须与属性需在同一行
  ```
  $('div').attr().width().height()
  $('div')
    .attr()
    .width()
    .height()
  ```
  * 行尾不要加分号
    * window.alert('hi'); // ✗ avoid
## 引号
  * 除需要转义的情况外，字符串统一使用单引号
  ```
  console.log('hello there')
  $("<div class='box'>")
  ```
## import引入文件
  * import引入的文件使用小驼峰命名方式
    * import dolphinCommon from 'dolphin_common'
  * 同一模块有多个导入时一次性写完
  ```
  import { myFunc1 } from 'module'
  import { myFunc2 } from 'module'          // ✗ avoid

  import { myFunc1, myFunc2 } from 'module' // ✓ ok
  ```

# css代码规范

[参考](http://nec.netease.com/case)

## 命名规则
- 使用类选择器，放弃ID选择器
  - ID在一个页面中的唯一性导致了如果以ID为选择器来写CSS，就无法重用
- 防止污染和被污染
  - 如果你的模块或元件可能嵌套或被嵌套于其他模块或元件，那么要慎用标签选择器(slot template)
  - vue单文件css一定要加scoped!!!
  - 如果要覆盖其他组件生成的class样式, 一定要包在自己文件的父类里面, 防止对其他地方组件引用的样式影响
- BEM命名规范   [参考](https://blog.csdn.net/love_lovelove/article/details/79462952)
  - block-name__element-name--modifier
## 代码格式
- 省略值为0时的单位
  - 为节省不必要的字节同时也使阅读方便，我们将0px、0em、0%等值缩写为0
  - .m-box{margin:0 10px;background-position:50% 0;}
- 使用16进制表示颜色值时尽量缩写
  - .m-box{color:#f00;}
- 根据属性的重要性按顺序书写
  - 只遵循横向顺序即可，先显示定位布局类属性，后盒模型等自身属性，最后是文本类及修饰类属性

    显示属性 | 自身属性 | 文本属性和其他修饰
    ------|------|----------
    display | width | font
    visibility | height | text-align
    position | margin | text-decoration
    float | padding | vertical-align
    clear | border | white-space
    list-style | overflow | color
    top | min-width | background
  - 如果属性间存在关联性，则不要隔开写
    ```
    /* 这里的height和line-height有关联性 */
    .m-box{position:relative;height:20px;line-height:20px;padding:5px;color:#000;}
    ```
- 注释格式
  - 对选择器的注释统一写在被注释对象的上一行，对属性及值的注释写于分号后
  - 注释内容两端需空格，已确保即使在编码错误的情况下也可以正确解析样式
  - 在必要的情况下，可以使用块状注释，块状注释保持统一的缩进对齐
  ```
  /* 块状注释文字
   * 块状注释文字
   * 块状注释文字
   */
  .m-list li{
    height:20px;
    line-height:20px;/* 这里是对line-height的一个注释 */
    overflow:hidden;
  }
  /* 单行注释文字 */
  .m-list li em{color:#666;}
  ```

# html代码规范
- 建议每个元素都要添加关闭标签
- 图片通常使用 alt 属性。 在图片不能显示时，它能替代图片显示。
- 保持良好的树形结构
  - 每一个块级元素都另起一行，每一行都是缩进对齐
- 属性全部小写
  - 同时使用大小写是非常不好的习惯。
  - 开发人员通常使用小写 (类似 XHTML)。
  - 小写风格看起来更加清爽。
  - 小写字母容易编写。
- 属性值建议统一使用双引号
  - 如果属性值含有空格需要使用引号。
  - 混合风格不推荐的，建议统一风格。
  - 属性值使用引号易于阅读。
- 等号前后可以使用空格, 但推荐少用空格
  ```
  <link rel = "stylesheet" href = "styles.css">

  <link rel="stylesheet" href="styles.css">
  ```
- 避免一行代码过长
  - 使用 HTML 编辑器，左右滚动代码是不方便的。
  - 每行代码尽量少于 80 个字符。
- 空行和缩进
  - 不要无缘无故添加空行。
  - 为每个逻辑功能块添加空行，这样更易于阅读。
  - 缩进使用两个空格，不建议使用 TAB。
  - 比较短的代码间不要使用不必要的空行和缩进。
  ```
  <!-- 不必要的空行和缩进: -->
  <body>

    <h1>菜鸟教程</h1>

    <h2>HTML</h2>

    <p>
      菜鸟教程，学的不仅是技术，更是梦想。
      菜鸟教程，学的不仅是技术，更是梦想。
    菜鸟教程，学的不仅是技术，更是梦想,
      菜鸟教程，学的不仅是技术，更是梦想。
    </p>

  </body>
  ```

# vue
[官网地址](https://cn.vuejs.org/v2/style-guide/#%E8%A7%84%E5%88%99%E5%BD%92%E7%B1%BB)
## 优先级A (必要的)
- 组件名应该始终是多个单词的，根组件 App 除外
  - 这样做可以避免跟现有的以及未来的 HTML 元素相冲突，因为所有的 HTML 元素名称都是单个单词的
- 组件的 data 必须是一个函数
  - 当在组件中使用 data 属性的时候 (除了 new Vue 外的任何地方)，它的值必须是返回一个对象的函数
- Prop 定义应该尽量详细
  - 在你提交的代码中，prop 的定义应该尽量详细，至少需要指定其类型
- 总是用 key 配合 v-for
- 避免 v-if 和 v-for 同时用在同一个元素上
- 对于应用来说，顶级 App 组件和布局组件中的样式可以是全局的，但是其它所有组件都应该是有作用域的
  - 使用 `scoped` 特性 : ```<style scoped>```
  - 使用 `CSS Modules` : ```<style module>```
- 在插件、混入等扩展中始终为自定义的私有属性使用 $_ 前缀。并附带一个命名空间以回避和其它作者的冲突 (比如 `$_yourPluginName_`)
## 优先级 B 的规则：强烈推荐 (增强可读性)
- 只要有能够拼接文件的构建系统，就把每个组件单独分成文件
- 单文件组件的文件名应该要么始终是单词大写开头 (PascalCase)，要么始终是横线连接 (kebab-case)
- 应用特定样式和约定的基础组件 (也就是展示类的、无逻辑的或无状态的组件) 应该全部以一个特定的前缀开头，比如 Base、App 或 V
- 单例组件
  - 只应该拥有单个活跃实例的组件应该以 The 前缀命名，以示其唯一性
- 密耦合的组件名
  - 和父组件紧密耦合的子组件应该以父组件名作为前缀命名
  - 如果一个组件只在某个父组件的场景下有意义，这层关系应该体现在其名字上
- 组件名应该以高级别的 (通常是一般化描述的) 单词开头，以描述性的修饰词结尾
  components/
  |- SearchButtonClear.vue
  |- SearchButtonRun.vue
  |- SearchInputQuery.vue
  |- SearchInputExcludeGlob.vue
  |- SettingsCheckboxTerms.vue
  |- SettingsCheckboxLaunchOnStartup.vue
- 自闭合组件
  - 在单文件组件、字符串模板和 JSX 中没有内容的组件应该是自闭合的——但在 DOM 模板里永远不要这样做。

    自闭合组件表示它们不仅没有内容，而且刻意没有内容。其不同之处就好像书上的一页白纸对比贴有“本页有意留白”标签的白纸。而且没有了额外的闭合标签，你的代码也更简洁。
    
    不幸的是，HTML 并不支持自闭合的自定义元素——只有官方的“空”元素。所以上述策略仅适用于进入 DOM 之前 Vue 的模板编译器能够触达的地方，然后再产出符合 DOM 规范的 HTML。
    ```
    反例
    <!-- 在单文件组件、字符串模板和 JSX 中 -->
    <MyComponent></MyComponent>
    <!-- 在 DOM 模板中 -->
    <my-component/>

    好例子
    <!-- 在单文件组件、字符串模板和 JSX 中 -->
    <MyComponent/>
    <!-- 在 DOM 模板中 -->
    <my-component></my-component>
    ```
- 模板中的组件名大小写
  - 对于绝大多数项目来说，在单文件组件和字符串模板中组件名应该总是 PascalCase 的——但是在 DOM 模板中总是 kebab-case 的
- JS/JSX 中的组件名大小写
  - JS/JSX 中的组件名应该始终是 PascalCase 的，尽管在较为简单的应用中只使用 Vue.component 进行全局组件注册时，可以使用 kebab-case 字符串
- 完整单词的组件名
  - 组件名应该倾向于完整单词而不是缩写
- Prop 名大小写
  - 在声明 prop 的时候，其命名应该始终使用 camelCase，而在模板和 JSX 中应该始终使用 kebab-case
  ```
  props: {
    greetingText: String
  }
  <WelcomeMessage greeting-text="hi"/>
  ```
- 多个特性的元素
  - 多个特性的元素应该分多行撰写，每个特性一行

- 模板中简单的表达式
  - 组件模板应该只包含简单的表达式，复杂的表达式则应该重构为计算属性或方法
- 简单的计算属性
  - 应该把复杂计算属性分割为尽可能多的更简单的属性
  ```
  反例
  computed: {
    price: function () {
      var basePrice = this.manufactureCost / (1 - this.profitMargin)
      return (
        basePrice -
        basePrice * (this.discountPercent || 0)
      )
    }
  }
  好例子
  computed: {
    basePrice: function () {
      return this.manufactureCost / (1 - this.profitMargin)
    },
    discount: function () {
      return this.basePrice * (this.discountPercent || 0)
    },
    finalPrice: function () {
      return this.basePrice - this.discount
    }
  }
  ```
- 带引号的特性值
  - 非空 HTML 特性值应该始终带引号 (单引号或双引号，选你 JS 里不用的那个)
  ```
  <AppSidebar :style="{ width: sidebarWidth + 'px' }">
  ```
- 指令缩写
  - 指令缩写 (用 : 表示 v-bind: 和用 @ 表示 v-on:) 应该要么都用要么都不用

## 优先级 C 的规则：推荐 (将选择和认知成本最小化)
- 组件/实例的选项应该有统一的顺序。

  这是我们推荐的组件选项默认顺序。它们被划分为几大类，所以你也能知道从插件里添加的新属性应该放到哪里。

  - 副作用 (触发组件外的影响)
    - el
  - 全局感知 (要求组件以外的知识)
    - name
    - parent
  - 组件类型 (更改组件的类型)
    - functional
  - 模板修改器 (改变模板的编译方式)
    - delimiters
    - comments
  - 模板依赖 (模板内使用的资源)
    - components
    - directives
    - filters
  - 组合 (向选项里合并属性)
    - extends
    - mixins
  - 接口 (组件的接口)
    - inheritAttrs
    - model
    - props/propsData
  - 本地状态 (本地的响应式属性)
    - data
    - computed
  - 事件 (通过响应式事件触发的回调)
    - watch
  - 生命周期钩子 (按照它们被调用的顺序)
    - beforeCreate
    - created
    - beforeMount
    - mounted
    - beforeUpdate
    - updated
    - activated
    - deactivated
    - beforeDestroy
    - destroyed
  - 非响应式的属性 (不依赖响应系统的实例属性)
    - methods
  - 渲染 (组件输出的声明式描述)
    - template/render
    - renderError
- 元素 (包括组件) 的特性应该有统一的顺序
    - 定义 (提供组件的选项)
      - is
    - 列表渲染 (创建多个变化的相同元素)
      - v-for
    - 条件渲染 (元素是否渲染/显示)
      - v-if
      - v-else-if
      - v-else
      - v-show
      - v-cloak
    - 渲染方式 (改变元素的渲染方式)
      - v-pre
      - v-once
    - 全局感知 (需要超越组件的知识)
      - id
    - 唯一的特性 (需要唯一值的特性)
      - ref
      - key
      - slot
    - 双向绑定 (把绑定和事件结合起来)
      - v-model
    - 其它特性 (所有普通的绑定或未绑定的特性)
    - 事件 (组件事件监听器)
      - v-on
    - 内容 (覆写元素的内容)
      - v-html
      - v-text
- 组件/实例选项中的空行

  你可能想在多个属性之间增加一个空行，特别是在这些选项一屏放不下，需要滚动才能都看到的时候。当你的组件开始觉得密集或难以阅读时，在多个属性之间添加空行可以让其变得容易。
  ```
  props: {
    value: {
      type: String,
      required: true
    },

    focused: {
      type: Boolean,
      default: false
    },

    label: String,
    icon: String
  },

  computed: {
    formattedValue: function () {
      // ...
    },

    inputClasses: function () {
      // ...
    }
  }
  ```
- 单文件组件的顶级元素的顺序

  单文件组件应该总是让 `script`、`template` 和 `style` 标签的顺序保持一致。且 `style` 要放在最后，因为另外两个标签至少要有一个
## 优先级 D 的规则：谨慎使用 (有潜在危险的模式) 
- 没有在 v-if/v-else-if/v-else 中使用 key 谨慎使用

  如果一组 v-if + v-else 的元素类型相同，最好使用 key

  默认情况下，Vue 会尽可能高效的更新 DOM。这意味着其在相同类型的元素之间切换时，会修补已存在的元素，而不是将旧的元素移除然后在同一位置添加一个新元素。如果本不相同的元素被识别为相同，则会出现意料之外的结果 
  ```
  <div
  v-if="error"
  key="search-status"
  >
    错误：{{ error }}
  </div>
  <div
    v-else
    key="search-results"
  >
    {{ results }}
  </div>
  ```
- scoped 中的元素选择器

  元素选择器应该避免在 scoped 中出现。在 scoped 样式中，类选择器比元素选择器更好，因为大量使用元素选择器是很慢的。
  ```
  反例
  <template>
    <button>X</button>
  </template>

  <style scoped>
  button {
    background-color: red;
  }
  </style>
  好例子
  <template>
    <button class="btn btn-close">X</button>
  </template>

  <style scoped>
  .btn-close {
    background-color: red;
  }
  </style>
  ```
- 隐性的父子组件通信

  应该优先通过 prop 和事件进行父子组件之间的通信，而不是 this.$parent 或改变 prop。

- 非 Flux 的全局状态管理

  应该优先通过 Vuex 管理全局状态，而不是通过 this.$root 或一个全局事件总线。

  通过 this.$root 和/或全局事件总线管理状态在很多简单的情况下都是很方便的，但是并不适用于绝大多数的应用。Vuex 提供的不仅是一个管理状态的中心区域，还是组织、追踪和调试状态变更的好工具。

# ES6
[w3c](https://www.w3cschool.cn/ecmascript/gvcr1q64.html)
## 块级作用域
- let 取代 var
- 全局常量和线程安全
  - 在let和const之间，建议优先使用const，尤其是在全局环境，不应该设置变量，只应设置常量。

## 字符串
- 静态字符串一律使用单引号或反引号，不使用双引号。动态字符串使用反引号

## 解构赋值
- 使用数组成员对变量赋值时，优先使用解构赋值
  ```
  const arr = [1, 2, 3, 4];

  // bad
  const first = arr[0];
  const second = arr[1];

  // good
  const [first, second] = arr;
  ```
- 函数的参数如果是对象的成员，优先使用解构赋值
  ```
  // bad
  function getFullName(user) {
    const firstName = user.firstName;
    const lastName = user.lastName;
  }

  // good
  function getFullName(obj) {
    const { firstName, lastName } = obj;
  }

  // best
  function getFullName({ firstName, lastName }) {
  }
  ```
- 如果函数返回多个值，优先使用对象的解构赋值，而不是数组的解构赋值。这样便于以后添加返回值，以及更改返回值的顺序
  ```
  // bad
  function processInput(input) {
    return [left, right, top, bottom];
  }

  // good
  function processInput(input) {
    return { left, right, top, bottom };
  }

  const { left, right } = processInput(input);
  ```
## 对象
- 单行定义的对象，最后一个成员不以逗号结尾。多行定义的对象，最后一个成员以逗号结尾
  ```
  // bad
  const a = { k1: v1, k2: v2, };
  const b = {
    k1: v1,
    k2: v2
  };

  // good
  const a = { k1: v1, k2: v2 };
  const b = {
    k1: v1,
    k2: v2,
  };
  ```
- 对象尽量静态化，一旦定义，就不得随意添加新的属性。如果添加属性不可避免，要使用Object.assign方法。
  ```
  // bad
  const a = {};
  a.x = 3;

  // if reshape unavoidable
  const a = {};
  Object.assign(a, { x: 3 });

  // good
  const a = { x: null };
  a.x = 3;
  ```
- 如果对象的属性名是动态的，可以在创造对象的时候，使用属性表达式定义
  ```
  // bad
  const obj = {
    id: 5,
    name: 'San Francisco',
  };
  obj[getKey('enabled')] = true;

  // good
  const obj = {
    id: 5,
    name: 'San Francisco',
    [getKey('enabled')]: true,
  };
  ```
  上面代码中，对象obj的最后一个属性名，需要计算得到。这时最好采用属性表达式，在新建obj的时候，将该属性与其他属性定义在一起。这样一来，所有属性就在一个地方定义了。

- 对象的属性和方法，尽量采用简洁表达法，这样易于描述和书写
  ```
  var ref = 'some value';

  // bad
  const atom = {
    ref: ref,

    value: 1,

    addValue: function (value) {
      return atom.value + value;
    },
  };

  // good
  const atom = {
    ref,

    value: 1,

    addValue(value) {
      return atom.value + value;
    },
  };
  ```
## 数组
- 使用扩展运算符（...）拷贝数组
  ```
  // bad
  const len = items.length;
  const itemsCopy = [];
  let i;

  for (i = 0; i < len; i++) {
    itemsCopy[i] = items[i];
  }

  // good
  const itemsCopy = [...items];
  ```
- 使用Array.from方法，将类似数组的对象转为数组
  ```
  const foo = document.querySelectorAll('.foo');
  const nodes = Array.from(foo);
  ```
## 函数
- 立即执行函数可以写成箭头函数的形式
  ```
  (() => {
    console.log('Welcome to the Internet.');
  })();
  ```
- 那些需要使用函数表达式的场合，尽量用箭头函数代替。因为这样更简洁，而且绑定了this
  ```
  // bad
  [1, 2, 3].map(function (x) {
    return x * x;
  });

  // good
  [1, 2, 3].map((x) => {
    return x * x;
  });

  // best
  [1, 2, 3].map(x => x * x);
  ```
- 箭头函数取代Function.prototype.bind，不应再用self/_this/that绑定 this

  简单的、单行的、不会复用的函数，建议采用箭头函数。如果函数体较为复杂，行数较多，还是应该采用传统的函数写法。
  ```
  // bad
  const self = this;
  const boundMethod = function(...params) {
    return method.apply(self, params);
  }

  // acceptable
  const boundMethod = method.bind(this);

  // best
  const boundMethod = (...params) => method.apply(this, params);
  ```
- 所有配置项都应该集中在一个对象，放在最后一个参数，布尔值不可以直接作为参数
  ```
  // bad
  function divide(a, b, option = false ) {
  }

  // good
  function divide(a, b, { option = false } = {}) {
  }
  ```
- 不要在函数体内使用arguments变量，使用rest运算符（...）代替。因为rest运算符显式表明你想要获取参数，而且arguments是一个类似数组的对象，而rest运算符可以提供一个真正的数组。
  ```
  // bad
  function concatenateAll() {
    const args = Array.prototype.slice.call(arguments);
    return args.join('');
  }

  // good
  function concatenateAll(...args) {
    return args.join('');
  }
  ```
- 使用默认值语法设置函数参数的默认值
  ```
  // bad
  function handleThings(opts) {
    opts = opts || {};
  }

  // good
  function handleThings(opts = {}) {
    // ...
  }
  ```
## Map结构
- 注意区分Object和Map，只有模拟现实世界的实体对象时，才使用Object。如果只是需要key: value的数据结构，使用Map结构。因为Map有内建的遍历机制。
  ```
  let map = new Map(arr);

  for (let key of map.keys()) {
    console.log(key);
  }

  for (let value of map.values()) {
    console.log(value);
  }

  for (let item of map.entries()) {
    console.log(item[0], item[1]);
  }
  ```
## class
- 总是用Class，取代需要prototype的操作。因为Class的写法更简洁，更易于理解
  ```
  // bad
  function Queue(contents = []) {
    this._queue = [...contents];
  }
  Queue.prototype.pop = function() {
    const value = this._queue[0];
    this._queue.splice(0, 1);
    return value;
  }

  // good
  class Queue {
    constructor(contents = []) {
      this._queue = [...contents];
    }
    pop() {
      const value = this._queue[0];
      this._queue.splice(0, 1);
      return value;
    }
  }
  ```
- 使用extends实现继承，因为这样更简单，不会有破坏instanceof运算的危险
  ```
  // bad
  const inherits = require('inherits');
  function PeekableQueue(contents) {
    Queue.apply(this, contents);
  }
  inherits(PeekableQueue, Queue);
  PeekableQueue.prototype.peek = function() {
    return this._queue[0];
  }

  // good
  class PeekableQueue extends Queue {
    peek() {
      return this._queue[0];
    }
  }
  ```
## 模块
- 首先，Module语法是JavaScript模块的标准写法，坚持使用这种写法。使用import取代require
- 使用export取代module.exports

  如果模块只有一个输出值，就使用export default，如果模块有多个输出值，就不使用export default，不要export default与普通的export同时使用。
  ```
  // commonJS的写法
  var React = require('react');

  var Breadcrumbs = React.createClass({
    render() {
      return <nav />;
    }
  });

  module.exports = Breadcrumbs;

  // ES6的写法
  import React from 'react';

  const Breadcrumbs = React.createClass({
    render() {
      return <nav />;
    }
  });

  export default Breadcrumbs
  ```
- 不要在模块输入中使用通配符。因为这样可以确保你的模块之中，有一个默认输出（export default）
  ```
  // bad
  import * as myObject './importModule';

  // good
  import myObject from './importModule';
  ```
- 如果模块默认输出一个函数，函数名的首字母应该小写
  ```
  function makeStyleGuide() {
  }

  export default makeStyleGuide;
  ```
- 如果模块默认输出一个对象，对象名的首字母应该大写
  ```
  const StyleGuide = {
    es6: {
    }
  };

  export default StyleGuide;
  ```
