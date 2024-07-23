# Vue 3 框架 前端笔记



### 使用 Vite 快速构建项目

1. **创建项目（建议使用 cmd 终端，且使用 yarn）：yarn create vite（根据选项提示进行选择）**

   **补充命令：**

   **（一）创建普通 vue 项目：yarn create vite 项目名称 --template vue**

   **（二）创建基于ts模板的项目：yarn create vite 项目名称 --template vue-ts**

2. **按照提示，yarn（生成 node_modules）并且 yarn dev 启动热更新服务器**

3. **在最终的生产环境中，yarn serve 可以预览打包后的 dist 文件项目**

4. **进阶用法：终端输入 code .（快速在 VS Code 打开当前项目）** 

**最新的基于 vite 的 create vue 命令创建项目（根据选项提示进行选择，选项更多，包含Router、Eslint、Pinia）：yarn create vue**

**尤其注意：使用最新的创建项目命令，创建的项目无需额外配置 @ 路径，默认可以直接使用**



### Vue3 框架

**Vue2 是 “ 选项式 API ”，Vue3 是 “ 组合式 API ”，Vue3 的底层逻辑使用 TS 进行重构，且所有内容写在 setup 函数中，前面声明的变量或者方法，后面会立即使用，因此不再需要 this，每个功能的逻辑写在一起，形成功能模块化，更加便于维护，另外数据响应式（双向数据绑定）的原理，采取 ES6 的 proxy 替代 ES5  的 Object.defineProperty，原来检测对象属性的变化, 需要对每个属性进行递归监听，而 proxy 可以对整个对象进行劫持，最后 TS 的使用，规范了类型检测，代码更加稳定，且 vite 的加入，使得不再需要传统的 webpack 实时打包，而是在浏览器中模块化懒加载（只加载当前访问页面内容），速度更快**



#### 一、Vue3 基础语法

##### （一）setup 函数

**原先在 data 中定义的变量，或者在 methods 里定义的方法，改写成如下形式，需要在 setup 函数中进行声明变量或者方法，然后必须 return 一个对象，对象中包含这些变量和方法，才能供 template 模板使用**

```javascript
// 使用 ref 或者 reactive 需要提前导入相关方法
import { ref , reactive } from 'vue' 

export default {
  setup () {
    // 声明的变量需要进行包装，才可以变成响应式，ref 包装简单数据
    let 简单vue变量 = ref（初始值）
    
    // 声明的变量需要进行包装，才可以变成响应式，reactive 包装复杂数据
    let 复杂vue变量 = reactive (
        // 复杂数据（对象）
        {
            属性1:初始值,
            属性2:初始值
        }
    )
    
    let 方法名 = () => { 此处为处理程序 }
    
    // 所有定义的变量、方法都需要 return 出去才可以使用
    return {
        简单vue变量,
        复杂vue变量,
        方法名,
    }
  }
}
```



##### （二）数据的响应式处理

**声明的变量需要进行包装（包裹），才可以变成响应式（ref 包装简单数据，reactive 包装复杂数据），示例，详见（一）setup 函数**

**前提条件：使用 ref 或者 reactive 需要提前导入该方法：import { ref , reactive } from 'vue'**

1. **简单数据（ref）的取值**

   **ref 包装的简单数据，在 setup 函数中需要使用 " vue变量.value " 进行数据操作，在 template 模板中直接使用 “ vue变量 ” 访问（ref 本质是在数据外面嵌套了一层对象，真正的数据存在于该对象的 value 属性中，因此在 setup 函数中，需要精准的访问该对象的 value 值，通过 " vue变量.value " 进行访问，而 template 模板中会对该变量自动解套，不需要额外的 .value，因此可以直接通过 ” vue变量 “ 访问）**

   **注意：ref 实际上也能包裹复杂数据，在复杂数据的外面再次嵌套一层对象，通过 .value 的方式进行深层访问，略微麻烦，但可以提高性能**

   **尤其注意（强调）：实际开发中，建议使用 ref 包装复杂数据，通常情况，当对后端返回的数据进行赋值到变量中，使用 ref 包装的变量，赋值的只是该变量的 value 值，仍具有响应特性，但是使用 reactive，会对整体进行赋值，外层的 reactive 函数会被覆盖掉，因此失去响应性**

2. **复杂数据（reactive）的取值**

   **reactive 包装的复杂数据，无论是在 setup 函数中，还是在 template 模板中，都可以直接通过 " vue变量.属性名 " 进行访问**



##### （三）数据解构后丢失响应式的解决办法

**无论是 ref 还是 reactive 进行包装的 ” 复杂数据 “（ref 包装的数据需要使用额外的 .value 进行取值），对该数据进行解构后，都会丢失响应式，因此可使用 toRefs 函数对结构出来的数据单独做响应式处理（toRefs 本质是将解构出来的数据统统转换成 ref 包装的形式，因此解构出来的数据，仍然需要 .value 访问）**

**前提条件：使用 toRefs 需要提前导入该方法：import { toRefs } from 'vue'**

```javascript
const 复杂vue变量 = ref或reactive(
      // 复杂数据（对象）
        {
            属性1:初始值,
            属性2:初始值
        }
)

// 使用 ref 包装复杂数据
const ref包装的vue变量 = ref (
        // 复杂数据（对象）
        {
            属性1:初始值,
            属性2:初始值
        }
)
// 使用 reactive 包装复杂数据
const reactive包装的vue变量 = reactive (
        // 复杂数据（对象）
        {
            属性1:初始值,
            属性2:初始值
        }
    )

// 由于解构出来的数据（属性1、属性2）具有 ref 特性，因此在 script 里必须使用额外的 .value 访问，在 template 模板中直接访问
const { 属性1, 属性2 } = toRefs(ref包装的vue变量.value)
const { 属性1, 属性2 } = toRefs(reactive包装的vue变量)
```



##### （四）script-setup 语法（setup 进阶）

**传统的 setup 函数，需要写在 export default 中，且需要将变量或者方法等 return 出去，略显麻烦，因此可使用 script-setup 语法**

**注意：在 script 标签上书写 setup 属性，丢弃 export default 和 return ，同时也不需要传统的 setup 函数，另外 template 模板位置写在 script 标签的后面**

```html
<script setup>
    // 使用 ref 或者 reactive 需要提前导入相关方法
    import { ref , reactive } from 'vue' 
    
    // 声明的变量需要进行包装，才可以变成响应式，ref 包装简单数据
    let 简单vue变量 = ref（初始值）
    
    // 声明的变量需要进行包装，才可以变成响应式，reactive 包装复杂数据
    let 复杂vue变量 = reactive (
        // 复杂数据（对象）
        {
            属性1:初始值,
            属性2:初始值
        }
    )
    
    let 方法名 = () => { 此处为处理程序 }
</script>

<template>
  模板位置写在后面
</template>
```



##### （五）computed 计算属性

**前提条件：使用 computed 需要提前导入该方法：import { computed } from 'vue' **

**计算属性（可实时更新和计算的变量），直接定义在 script-setup 中即可：**

1. **低阶语法：**

   **const 计算属性名 = computed（（） => { 处理程序；return 处理结果 } ）**

2. **完整语法：**

   **注意：由于 v-model 绑定计算属性，表单只能获取计算属性的值，但不能给计算属性赋值，此时采取完整语法**

```javascript
    const 计算属性名 = computed({
      //对计算属性进行赋值时（在复选框中是通过勾选赋值true或者false，其余情况：“计算属性=某个值” 赋值），调用set函数
      set(val) {
        // val是对计算属性赋值时，传递过来的参数（在复选框中是true或者false，在其余情况是某个值）
        利用val执行某些操作;
      },
      //当别的地方需要获取计算属性时，调用get函数
      get() {
        return 处理结果（值）;
      },
    });
```



##### （六）watch 侦听器

**前提条件：使用 watch 需要提前导入该方法：import { watch } from 'vue' **

**侦听器，直接定义在 script-setup 中即可：**

**（Ⅰ）简单数据侦听**

1. **侦听单个简单变量：**

   **watch ( 简单vue变量，(newVal，oldVal) => { 此处为处理程序 } )**

2. **侦听多个简单变量（实际开发中，通常不会选择该方式）：**

```JavaScript
watch ([简单vue变量1, 简单vue变量2], (newVal，oldVal) => {
  // newVal和oldVal实际上是数组形式，newVal保存多个变量的新值，oldVal保存多个变量的旧值，通常使用数组解构拿到想要的数据
  const [简单vue变量1的新值,简单vue变量2的新值] = newVal
  const [简单vue变量1的旧值,简单vue变量2的旧值] = oldVal
})
```

3. **侦听对象里面的某个属性（对象中的单个属性属于简单数据）：**

   **如果一个复杂vue变量，保存的是对象，不能书写 “ 复杂vue变量.xxx.xxx ” 的形式，该形式是取值，并不代表是该属性，可采取以下方式（固定写法）：**

   **watch (（）=> 复杂vue变量.xxx.xxx ，(newVal，oldVal) => { 此处为处理程序 } )**



**（Ⅱ）复杂数据侦听（侦听对象或者数组的变化）**

1. **reactive 包装的复杂数据侦听（默认侦听的是整个对象，可以直接侦听到所有属性，无需深度侦听）：**

   **watch ( reactive包装的复杂vue变量，(newVal，oldVal) => { 此处为处理程序 } )**

2. **ref 包装的复杂数据侦听（默认侦听的是复杂数据的地址，对象属性值发生变化，地址并不会改变，因此需要深度侦听）**

```javascript
watch (ref包装的复杂vue变量, (newVal, oldVal) => {}, {
  // 开启深度侦听  
  deep: true,
  // 立即执行
  immediate: true,
});
```



##### （七）组件的局部使用及全局进阶

**Ⅰ、局部使用组件**

**Vue2 中需要对组件进行引入并注册，才可以使用，但在 Vue3 中，引入组件后可以直接使用，无需注册**

**第一步 ：在 script-setup 中，引入该组件**

```javascript
import 组件名 from "组件路径";
```

**第二步：在 template 模板中直接以组件名作为组件标签名即可**

```html
<template>
  <组件名></组件名>
</template>
```

**尤其注意：只有在 script-setup 语法下，才能实现组件无需注册，仅引入就可使用**



**Ⅱ、组件的全局注册及进阶用法**

**常规用法：创建组件并在 main.js 文件通过 import 引入该组件，使用 app.component（ “ 组件标签名 ” ，引入的组件名 ）进行全局注册**

**进阶用法：**

1. **在 src/components 文件夹下新建 index.js 文件，在该文件中引入所有需要全局注册的组件，并默认导出一个 install 方法，里面进行组件的全局注册**

```javascript
// 所有需要全局注册的组件，都在这里引入
import 组件名 from '组件所在路径'

// 如果是 Typescript 需要引入 App，用于给 install 里的 app 指定类型（JavaScript 中可省略）
import { App } from 'vue'

export default {
  // Typescript 中需要给 app 指定类型（JavaScript 中可省略）
  install(app: App) {
    // 所有需要全局注册的组件，都在这里进行全局注册
    app.component('组件标签名', 引入的组件名)
  }
}
```

2. **在 main.js 文件中默认导入上面的 index.js 文件，并使用 app.use 方法执行这个默认导出的对象，app.use 会执行该对象中的 install 方法**

```javascript
import Component from '@/components'
app.use(Component)
```



**Ⅲ、Typescript 中全局注册的组件，提供额外类型声明**

**原因：当处于 Typescript 中，VsCode 无法识别全局注册（强调：必须是全局注册）的组件，需要进行额外的类型声明**

**在 src 下新建一个根级别的 global.d.ts 文件，写入如下代码：**

```typescript
// 引入全局注册的组件，在此处进行全局组件的类型声明
import 组件标签名 from '组件所在路径'

declare module 'vue' {
  export interface GlobalComponents {
    组件标签名: typeof 组件标签名
  }
}
```



##### （八）组件通信

**（Ⅰ）父传子**

**第一步（父组件传递）：在父组件中的子组件标签名上，父组件传递数据的语法与 Vue2 保持一致**

```html
  <子组件标签名 :父组件传递的自定义变量名 = 'vue变量'></子组件标签名>
```

**第二步（子组件接收）：在 script-setup 中使用 defineProps 函数进行接收，该函数传参是一个对象，里面的接收名可以写成对象形式，进行相关限定**

```javascript
const props = defineProps({
  父组件传递的自定义变量名（也就是自定义接收名）: {
      类型限定、默认内容、是否必传
  },
})
```

**尤其注意：script 标签中需要使用 “ props.自定义接收名 ” 进行访问，template 模板中直接通过 ” 自定义接收名 “ 即可访问**

**尤其重要：在 vue3 中，使用 v-model 语法糖，进行父传子，可实现等价于父传子并且 .sync 的效果，子组件通过 update 进行更新即可（仍然需要使用 defineEmits 方法事先声明自定义事件名称，且名称必须为 ’update:modelValue'，只不过该自定义事件比较特殊，无需在父组件中绑定该自定义事件）**

**补充：使用 `v-model = vue变量` 传递数据，等价于 `v-model:modelValue = vue变量`，子组件中 props 接收的名称必须为 modelValue，且自定义事件名称必须为 `update:modelValue`（必须保持一致），若想自定义名称，可使用 `v-model:自定义名称 = vue变量`，子组件中 props 接收的名称就可以是该自定义名称，自定义事件名称则必须为 `update:自定义名称`（必须保持一致），且前缀 `update:` 不可省略（固定写法）**



**（Ⅱ）子传父**

**第一步（子组件传递前的事件声明）：首先需要提前声明子组件向父组件提交的自定义事件，并获取 emit 方法**

```javascript
const emit = defineEmits(['子传父的自定义事件名称','其它子传父的自定义事件名称'])
```

**第二步（子组件传递）：通过点击事件等，触发事件处理函数，调用 emit 方法，传递相关数据**

```javascript
const 事件处理函数 = () => {
  emit('子传父的自定义事件名称',需要传递的数据 )
}
```

**第三步（父组件接收）：在父组件中的子组件标签名上，接收语法与 Vue2 保持一致**

```html
  <子组件标签名 @子传父的自定义事件名称 = '父组件的事件处理函数名称'></子组件标签名>
```

**第四步：该事件处理函数的参数，就是传递过来的数据**

```javascript
const 父组件的事件处理函数名称 = (子组件传递过来的数据) => {
  依赖子组件传递过来的数据，进行相关处理
}
```



**（Ⅲ）依赖注入（跨组件传递）**

**第一步：父组件使用 provide 提供数据或者方法**

1. **前提条件：使用 provide 需要提前导入该方法：import { provide } from 'vue' **
2. **提供数据或方法：provide（ “ 自定义传递名称（一般与数据名或者方法名保持一致）”，需要传递的数据名（vue变量）或者方法名 ）**

**第二步：子组件乃至孙组件使用 inject 接收数据或者方法**

1. **前提条件：使用 inject 需要提前导入该方法：import { inject } from 'vue' **
2. **接收数据或方法：const 接收名 = inject（ “ 自定义传递名称 ” ）**

**注意：按照规范，该接收名一般与传递过来的数据名（vue变量）或方法名保持一致，同时与传递名称保持一致**

**尤其注意：使用依赖注入的原则就是，父级定义的数据，必须在父级修改，但是可以把修改数据的方法传递给子组件进行调用**

**及其重要：当依赖注入和 TS 结合使用时，inject 接收到的数据（接收名），需要在 inject 的后面使用尖括号中声明它的类型，如果接收的是一个方法，在调用时（强调）需要在方法名后面使用非空断言**



##### （九）配合 ref 进行 DOM 绑定或组件绑定

1. **使用 ref 需要提前导入该方法（ 通常情况在响应式包装时就已经导入 ref ）：import { ref } from 'vue'**

2. **定义一个变量（用于绑定 DOM 或者组件）：const 名称变量（规范：DOM名称或者组件名称+Ref） =  ref（null）**
3. **给标签或者组件添加属性：ref = “ 名称变量 ”**
4. **使用 “ 名称变量.value ” 获取绑定的 DOM 或者组件（尤其注意：如果只是 DOM 操作， 使用 “ 名称变量.value ” 可以直接进行 DOM 操作）**
5. **如果是调用组件内的相关数据或方法，需要在需要被调用的子组件内，使用 difineExpose 进行数据或方法的暴露**

```javascript
// 在子组件中，直接书写 defineExpose 方法，暴露数据或者方法给外部组件使用
defineExpose({
  // 此处为 ES6 中对象的简单写法
  数据名,
  方法名,
})
```



##### （十）Vue3 中的生命周期

**script-setup 就相当于 create 钩子函数**

**onMounted（（）=> { 在 onMounted 钩子函数中，可以获取 DOM 元素，注册全局事件 } ）**

**onBeforeUnmount（（）=> { 当组件销毁时触发，用于移除全局事件 } ）**

**注意：使用这些钩子函数，需要提前引入相关方法（可利用 VsCode 的自动导入功能）**



##### （十一）自定义指令的全局注册

**常规用法：在 main.js 文件中，通过 app.directive（"自定义指令名称"，{  mounted（el，options）{ el是当前指令绑定的元素，options.value是传递过来的值 }  } ）来注册全局自定义指令**

**进阶用法：**

1. **在 src/directives 文件夹下新建 index.js 文件，默认导出一个 install 方法，里面进行自定义指令的全局注册**

```JavaScript
// 如果是 Typescript 需要引入 App，用于给 install 里的 app 指定类型（JavaScript 中可省略）
import { type App } from 'vue'

export default {
  // Typescript 中需要给 app 指定类型（JavaScript 中可省略）
  install(app: App) {
    app.directive("自定义指令名称", {
      // Vue3 中使用 mounted 函数代替了 inserted 函数
      mounted(el, options) {
          // el是当前指令作用的 DOM 元素，options 是一个配置项，其中 options.value 接收的是（v-指令名称 = "变量"）中的变量值
          处理程序;
      },
    });
  },
};
```

2. **在 main.js 文件中默认导入上面的 index.js 文件，并使用 app.use 方法执行这个默认导出的对象，app.use 会执行该对象中的 install 方法**

```javascript
import directive from '@/directives'
app.use(directive)
```



#### 二、pinia 状态管理

**前提须知：pinia 是完全独立的，每个仓库内部（尤其注意是仓库内部），state 里的属性，统统都不需要使用 ref 或 reactive 包装，正常的定义数据即可，在actions 中通过 axios 请求的数据，如需解构，也无需担心解构失去响应式，直接解构即可，但是在组件（.vue 文件）中使用该仓库，并解构该仓库中 state 相关属性或 getters 相关计算属性，会失去响应式，需要进行额外处理（storeToRefs）**

**（Ⅰ）安装 pinia 项目依赖包**

```
yarn add pinia
```

**（Ⅱ）在 main.js 中挂载 pinia**

```javascript
// 前提条件：从 vue 中解构出 createApp 方法
import { createApp } from 'vue'
// 前提条件：导入根组件
import App from './App.vue'
// 前提条件：基于 createApp 方法，将根组件作为参数，创建根实例应用（app）
const app = createApp(App)
// 前提条件：基于根实例应用（app），挂载到视图上
app.mount('#app')

// 从 pinia 中解构出 createPinia 方法
import { createPinia } from 'pinia'
// 基于 createPinia 方法，创建 pinia
const pinia = createPinia()
// 基于根实例应用（app），使用 pinia
app.use(pinia)

// 可简写成如下形式
// createApp(App).use(pinia).mount('#app')
```

**（Ⅲ）在 src 下新建 store 文件夹，并创建多个  js 文件，每个 js 文件都是一个独立的模块（仓库），可以实现 pinia 的模块化，每个模块需要书写如下代码**

```JavaScript
// 从 pinia 中解构出 defineStore 方法
import { defineStore } from 'pinia'

// 基于 defineStore 方法，创建 “使用当前仓库的方法”，命名规范： use仓库名Store
const use仓库名Store = defineStore('仓库名（一般与当前文件名保持一致）', {
  // 该写法和 data 函数 return 出一个对象保持一致，为了防止变量污染，state 里面的数据只在当前仓库有效
  state: () => {
    return {
        state里的属性名：属性值
    }
  },
  getters: {
    计算属性名() {
      // 依赖 “this.state里的属性名” 进行相关处理
      return 处理结果
    },
  },
// actions 里可以书写同步代码，也可以书写异步代码
  actions: {
    方法名() {
      // 依赖 “this.state里的属性名” 进行相关处理
      处理程序
    },
  },
})

// pinia 的第二种写法（舍弃 state、getters、actions，但必须return）
const use仓库名Store = defineStore('仓库名（一般与当前文件名保持一致）', () => {
    const 变量名 = ref('')
    const 方法名 = () => {}
    return { 变量名, 方法名 }
})

// 默认导出 “使用当前仓库的方法”，注意，“use仓库名Store” 是一个方法，使用时需要加括号
export default use仓库名Store
```

**尤其注意：无论是在 getters 中，还是在 actions 中，可以直接通过 ” this.属性名 “ 访问 state 里的数据，pinia 模块化文件中，this 指的是当前 store**

**（Ⅳ）在组件中单独使用某个仓库**

```javascript
// 在某个 .vue 文件（组件）中默认导入 “user仓库名Store” 这个方法
import use仓库名Store from '仓库文件路径'
// 基于 “use仓库名Store” 创建仓库
const 仓库名 = use仓库名Store()
```

**调用方法（user仓库名Store() 这个方法会将仓库中的 state、getters、actions 内所有属性或者方法包装成对象并返回，这个返回的名称就是仓库名）：**

1. **使用 “ 仓库名.state里面的属性名 ” 调用 state 里的数据**
2. **使用 “ 仓库名.getters里面的计算属性名 ” 调用 getters 里的计算属性**
3. **使用 “ 仓库名.actions里面的方法名（） ” 调用 actions 里的方法**

**尤其注意：通常情况，当基于 “user仓库名Store” 方法创建仓库后，该仓库中会存在多个属性（state 或者 getters），因此可以对仓库名进行相关解构，但是会丢失响应式，因此需要导入 storeToRefs 函数，对仓库名进行包裹，实现响应式，同样，解构出来的数据是 ref 形式，需要使用额外的 .value 进行访问**

```javascript
// 首先导入 storeToRefs 函数，注意不是从 vue 中解构出 storeToRefs，而是从 pinia 中 解构出 storeToRefs（因为它是 pinia 独有的方法）
import { storeToRefs } from 'pinia'
// 当创建出仓库后（此处省略创建仓库的步骤），对仓库名中的属性（state 或者 getters）进行解构
const { state里的变量、getters里的计算属性 } = storeToRefs（仓库名）
// 针对方法，正常解构即可，无需 storeToRefs 包裹
const { actions里的方法 } = 仓库名
```

**尤其注意：解构仓库里的方法时，正常解构即可，无需（且一定不能）使用 storeToRefs 函数，否则会报错**

**（Ⅴ）pinia 模块化进阶**

1. **模块中调用模块：**

   **pinia 支持在一个模块（仓库）中访问另外一个模块（仓库），直接在当前仓库中，导入 “ user其它仓库名Store ” 这个方法，然后在合适的地方（getters 或者 actions）基于 “use其它仓库名Store” 创建仓库，调用时的语法与之前保持一致**

2. **模块集中化处理：**

   **如果在一个文件中，需要使用多个仓库，那么就需要频繁的基于 “use仓库名Store” 创建多个仓库，因此可以考虑在 store 文件夹下新建一个 modules 文件夹，将所有模块（仓库）丢在里面，然后在 store 文件夹下另外创建一个 index.js 文件，书写如下代码：**

```JavaScript
// 导入所有模块（仓库）的 “use仓库名Store” 方法
import use仓库名一Store from '仓库一的文件路径'
import use仓库名二Store from '仓库二的文件路径'

// 封装一个 useStore 方法
const useStore = () => {
  return {
    // 以对象的形式，保存多个仓库
    仓库名一: use仓库名一Store(),
    仓库名二: use仓库名二Store(),
  }
}

// 默认导出 useStore 方法，该方法调用后返回的是一个对象，后续可解构里面的仓库
export default useStore
```

**在组件或者模块（仓库）中使用这些仓库，先默认导入 userStore 这个方法，然后基于该方法解构出需要的仓库，最后基于仓库名解构出对应的属性或者方法**

```JavaScript
// 默认导入 userStore 方法
import useStore from './store'

// 基于 userStore 方法解构出需要的仓库
const { 仓库名一 , 仓库名二 } = useStore()

// 基于仓库名解构出对应的数据(尤其注意：使用 storeToRefs 需要提前导入相关函数，此处省略）
const { state里的变量、getters里的计算属性 } = storeToRefs（仓库名一或者仓库名二）

// 针对方法，正常解构即可，无需 storeToRefs 包裹
const { actions里的方法 } = 仓库名一或者仓库名二
```

**（Ⅵ）仓库的侦听**

**当从 useStore 种解构出仓库名后，可以对该仓库名进行侦听，当仓库中的数据发生变化时，会触发该侦听，做某些操作**

```javascript
仓库名.$subscribe(() => { 依赖仓库里的数据做某些操作 })
```

**（Ⅶ）pinia 的持久化插件**

1. **安装持久化插件：yarn add pinia-plugin-persistedstate**
2. **在 main.js 中挂载 pinia 的基础上，引入该插件，并使用该插件**

```typescript
import persist from 'pinia-plugin-persistedstate'

// 在原先基于 createPinia 方法，创建 pinia 的基础上，使用 persist
const pinia = createPinia() 转变为 ===> const pinia = createPinia().use(persist)
```

3. **在 state、getters、actions 的同级处，新增如下配置**

```typescript
  persist: [
    {
      paths: ['state 中需要本地存储的属性名'],
      key: '保存在本地存储中的自定义名称',
      storage: localStorage,
      // 如果是在 uniapp 中，需要按照以下方式配置 storage，使得多端兼容
      storage: {
		setItem(key, value) {
			uni.setStorageSync(key, value)
		},
		getItem(key) {
			return uni.getStorageSync(key)
		},
	  }
    }
  ]
```

4. **如果不是 pinia 的选项式写法，而是安装式写法，则作为 defineStore 方法的第三个参数进行配置即可**

```typescript
// pinia 的第二种写法（舍弃 state、getters、actions，但必须return）
const use仓库名Store = defineStore('仓库名（一般与当前文件名保持一致）', () => {
    const 变量名 = ref('')
    const 方法名 = () => {}
    return { 变量名, 方法名 }
},
{
  persist: [
    {
      paths: ['state 中需要本地存储的属性名'],
      key: '保存在本地存储中的自定义名称',
      storage: localStorage
    }
  ]
},
)
```



#### 三、Vue-Router 4.0

**前提条件：安装依赖包（yarn add vue-router）**

1. **在 src 下新建 router 文件夹，并新建 indxe.js 文件（作为路由根文件），书写如下代码：**

```javascript
import { createRouter, createWebHashHistory } from "vue-router";
// 引入组件，此处以 Layout 组件示例，该组件通常是一个项目的核心页面，被称之为布局组件（包含二级路由）
import Layout from "@/views/layout/index.vue";

const router = createRouter({
  // 哈希模式
  history: createWebHashHistory(),
  // 此处路由规则数组可以单独抽离（如果抽离出去的文件是ts文件，需要给该路由规则数组进行类型断言：as RouteRecordRaw[]）
  // 对路由规则数组进行类型断言前提，需要引入相关类型，无需额外下载类型包（import { RouteRecordRaw } from 'vue-router'）
  routes: [
    { path: "/", component: Layout },
    // 使用路由懒加载，此处以 login 组件示例
    { path: "/login", component: () => import("@/views/login/index.vue") },
  ],
});

// 默认导出该路由
export default router;
```

2. **在 main.js 中使用路由**

```javascript
// 默认导入路由
import router from "@/router/index";
// 使用路由(在挂载实例之前：app.mount("#app");)
app.use(router)
```

3. **在 App 根组件挂载一级路由出口**

```html
<script setup lang="js"></script>

<template>
  <router-view></router-view>
</template>

<style scoped></style>
```

**路由中 route（多用于接收传参）和 router（多用于路由跳转）在组件中的使用：**

```javascript
import { useRoute, useRouter } from "vue-router";
const route = useRoute();
const router = useRouter();
```

**尤其注意：如果是在 template 模板标签中，直接使用 $route 和 $router 即可**

**导出路由并挂载的另一种方式（插件函数方式）：**

1. **在 indxe.js 文件（路由根文件）中封装一个 “安装路由” 的插件函数并按需导出（插件函数一般由 setup 命名）**

```typescript
// 在 typescript 中，由于封装的插件函数需要 app 这个参数，因此需要额外引入类型，对该参数进行类型声明
import { App } from "vue";

export const setupRouter = (app:App) => {
  // 挂载路由
  app.use(router);
};

// 此处可省略 export default router;
```

2. **在 main.ts 中按需导入 setupRouter 函数，直接调用即可（main.js 文件的 `app.use(router);` 可省略）**

**补充：路由规则数组的单独抽离详解**

1. **在 router 文件夹下新建 routes.ts 文件（routes 这个单词代表路由规则数组），将规则数组单独抽离到此文件**

```typescript
// 在 typescript 中，需要对单独抽离出来的路由规则数组声明类型，因此需要提前引入相关类型
import { RouteRecordRaw } from "vue-router";

// 对路由规则数组进行类型断言（好处：可通过 vscode 的快捷键，ctrl+i 触发建议）
const routes = [{ path: "路径", component: 组件 }] as RouteRecordRaw[];

export default routes;
```

2. **在路由根文件导入 routes，替换原有的路由规则数组即可**

**补充：vue 3 中的路由权限守卫（return true 是放行，return " 路由路径 " 是拦截并强制跳转到某个路径）**

**`首先在项目根路径新建一个 permission.js 文件，写入如下代码，并在 main.ts 直接加载（执行）该文件即可：`**

**`需要提前下载进度条插件：npm i nprogress`**

```typescript
// 引入路由实例（注意是引入路由文件夹下根文件默认导出的路由实例）
import router from '@/router'
// 引入进度条插件
import NProgress from 'nprogress'
// 引入进度条样式
import 'nprogress/nprogress.css'
// 引入使用仓库的通用方法
import useStore from '@/stores'

// 路由前置守卫
router.beforeEach((to) => {
  // 首先开启进度条
  NProgress.start()
  // 拿到 user 仓库，后续使用该仓库中储存的 token
  const { user } = useStore()
  // 白名单
  const Whitelist: string[] = ['/login']
  // 如果不存在 token
  if (!user.token) {
    if (Whitelist.includes(to.path)) {
      return true
    } else {
      return '/login'
    }
  } else {
    // 如果存在 token
    if (to.path === '/login') {
      // 进一步判断，如果去往的是登录页，强制跳转到主页
      return '/home'
    } else {
      // 其余正常放行（如果需要做 RBAC 权限，则需要继续判断，如果没有用户信息，动态筛选路由并添加后，手动去往当前路由，如果有用户信息，正常放行）
      // 也就是将当前 return true 改造成 if else 的形式，多加一个判断，else 的情况才 return true 
      return true
    }
  }
})

// 路由后置守卫
router.afterEach(() => {
  // 关闭进度条
  NProgress.done()
})
```

**补充：监听路由的方法（当需要在某个组件中监听路由，获取相关信息，可采取该方法，如点击路由动态添加标签导航，如果在权限里实现该逻辑则比较冗余）**

```javascript
// 第一种方式（Vue3 新增的钩子函数）
import { onBeforeRouteUpdate } from "vue-router"
onBeforeRouteUpdate((to,from) => {})

// 当第一种方式无效时，选择通用的解决办法
import { watch } from 'vue'
watch(
  () => router.currentRoute.value,
  (newVal, oldVal) => {
    console.log(newVal, oldVal)
  },
  { deep: true, immediate: true }
)
```



#### 四、Vuex 4.0

**前提条件：安装依赖包（yarn add vuex）**

1. **在 src 下新建 store 文件夹，并新建 indxe.js 文件（作为仓库根文件），书写如下代码：**

```javascript
import { createStore } from 'vuex';

const store = createStore({
  // 该写法和 data 函数 return 出一个对象保持一致，为了防止变量污染
  state(){
      return{
          state里的属性名：属性值
      }
  },
  // 与原先的 Vuex 版本使用一致，可模块化
  mutation: {},
  actions: {},
  getters: {},
})

// 默认导出该仓库
export default store
```

2. **在 main.js 中使用仓库**

```javascript
// 默认导入仓库
import store from "@/store/index";
// 使用仓库(在挂载实例之前：app.mount("#app");)
app.use(store)
```

**在组件中使用 store 仓库：**

```javascript
import { useStore } from "vuex";
const store = useStore();

// 利用 store 正常调用仓库里的属性或方法即可
```



#### 五、Typescript 超集语言

**首先需要全局安装（每台电脑仅需安装一次） typescript 工具包（用于支持 TS 解码）：npm i -g typescript**

**验证是否安装成功：在终端输入命令 “ tsc –v ”（查看 typescript 版本）**

**手动将 TS 编译为 JS：在终端中输入命令 “ tsc 文件名.ts ”（此时，在同级目录中会出现一个同名的 JS 文件）**

**尤其注意：实际开发中，不需要手动的通过 tsc 把 ts 文件转成 js 文件，而是交给 webpack 或者 vite 来完成（利用 vite 创建基于ts模板的项目）**



##### （一）类型注解

**语法：let 变量名：类型 = 值**

**尤其注意：声明变量时，可以省略声明类型，它会依据你第一次赋的初始值类型，限定该变量的类型，并做后续推导（可以通过鼠标触摸该变量查看类型）**



##### （二）类型分类

**（Ⅰ）简单类型：let 变量名：number 或 string 或 boolean = 值 **

**注意：简单类型会根据具体的值进行类型推断，一般不需要特别声明**



**（Ⅱ）数组类型**

**数组类型的两种写法：**

```typescript
// number型数组
let 变量名: number[] = [1, 2, 3]
// string型数组
let 变量名: Array<string> = ['a', 'b', 'c']
```

**尤其注意：“ number型数组 ” 或 “ string型数组 ” 限定了数组内的元素只能为 number 或 string 类型（不能同时存在）**



**（Ⅲ）联合类型**

**概念：多个类型的组合，称之为联合类型**

**如果需要数组中既有 number 类型，又有 string 类型，需要使用联合类型（即：使用 | 将多个类型组合在一起）**

```typescript
let 变量名: (number | string)[] = [1, 'a', 2]
let 变量名: Array<number | string> = ['a', 'b', 'c']
```

**尤其注意：联合类型同样适用于简单类型（ let 变量名：number | string | boolean = 值 ）**



**（Ⅳ）type 类型别名**

**当联合类型比较复杂时，可以使用 type 关键字，创建一个类型别名单独保存这个联合类型**

1. **定义类型别名：**

```typescript
type 类型别名 = (number | string | boolean)[]
```

2. **使用类型别名：**

```typescript
let 变量名: 类型别名 = [1, 'a', true]
```



**（Ⅴ）函数类型**

**概念：本质是给函数的参数和返回值指定类型**

```typescript
// 普通函数
function 函数名(参数1: 类型, 参数2: 类型): 返回类型 {
  return 结果
}

// 箭头函数
const 函数名 = (参数1: 类型, 参数2: 类型): 返回类型 => {
  return 结果
}

// 没有返回值的箭头函数
const 函数名 = (参数1: 类型, 参数2: 类型): void => {
  程序处理，无返回值（无 return）
}

// 包含可选参数的普通函数
function 函数名(参数1: 类型, 参数2?: 类型): 返回类型 {
  return 结果
}
```

**尤其注意：返回类型需要搭配 return 使用，如果一个函数没有返回值（不需要 return），则返回类型为 void（表示没有返回值）**

**尤其注意：如果函数的参数可传可不传，则需要在参数名的后面（冒号的前面）加上一个 “ ? ” 号，表示可选参数，但必选参数一定要写在前面**

**箭头函数配合类型别名（类型别名只能配合箭头函数使用，普通函数不能使用类型别名）：**

1. **定义类型别名：**

```typescript
type 类型别名 = (参数1: 类型, 参数2: 类型) => 返回类型
```

2. **使用类型别名：**

```typescript
const 函数名：类型别名 = (参数1, 参数2) => {
  return 结果
}
```

**注意：当一个函数的参数接收的是回调函数时，需要给该参数限定类型（ 参数: (参数1: 类型, 参数2: 类型) => 返回类型 ）**



**（Ⅵ）对象类型**

**概念：本质是给对象中属性和方法指定类型**

```typescript
// 空对象
let 对象名: {} = {}

// 有属性的对象
let 对象名: { 属性名: 类型 } = {
  属性名: 值
}

// 既有属性又有方法的对象
let 对象名: { 属性名: 类型; 方法名（参数：类型）: 返回类型 } = {
  属性名: 值,
  方法名(参数) {}
}

// 拥有可选属性或方法的对象
let 对象名: { 属性名: 类型; 方法名（参数：类型）: 返回类型 } = {
  属性名?: 值,
  方法名?(参数) {}
}
```

**对象配合类型别名：**

1. **定义类型别名：**

```typescript
type 类型别名 = {
  属性名: 类型
  方法名(参数：类型): 返回类型
}
```

2. **使用类型别名：**

```typescript
let 对象名: 类型别名 = {
  属性名: 值,
  方法名(参数) {}
}
```

**进阶：当一个类型别名保存的是对象类型时，可以通过 “ keyof 类型别名 ” 获取它的所有属性，且是字面量类型的形式（ 属性1 | 属性2 | 属性3）**



**（Ⅶ）interface 接口（为对象指定类型，且只能用于对象，且一般使用对象都用 interface，其余情况使用 type）**

**在对象中，除了使用 type 定义类型别名，interface 也可以为对象指定类型，但 interface 只能用于对象（强调），而 type，不仅可以为对象指定类型，实际上可以为任意类型指定别名，实际开发中，优先选择 type**

**interface 接口的用法：**

```typescript
// 定义接口（指定对象类型，interface 只能用于对象）
interface 接口名 {
  属性名: 类型
  方法名(参数：类型): 返回类型
}

// 使用接口
let 对象名: 接口名 = {
  属性名: 值,
  方法名(参数) {}
}
```



**（Ⅷ）“ interface 接口继承 ” 和 “ type 继承 ”**

**概念：继承就是将 type 或 interface 定义的类型进行复用，重新定义一个新的类型**

1. **interface 接口继承：**

```typescript
interface 接口一 { 属性名1: 类型; 属性名2: 类型 }

// 定义一个接口二，其中某些属性与接口一是相同的，因此可以继承接口一的某些属性，无需重复书写，最后这个接口二会包含属性1、属性2、属性3
interface 接口二 extends 接口一 {
  属性名3: 类型
}
```

2. **type 继承：**

```typescript
type 类型别名一 = {
  属性名1: 类型
  属性名2: 类型
}

// 定义一个类型别名二，其中某些属性与接口一是相同的，因此可以继承接口一的某些属性，无需重复书写
type 类型别名二 = {
  属性名3: 类型
} & 类型别名一
```



**（Ⅸ）元组类型**

**概念：元组类型是固定数量的数组，并且每个元素的类型严格对应，不能改变**

```typescript
let 变量名: [类型, 类型] = [元素1, 元素2]
```



**（Ⅹ）字面量类型和枚举类型**

1. **字面量类型：**

**概念：配合联合类型（且一定要配合联合类型使用，也就是竖杠，最终组合成字面量联合类型），自定义多个固定值，只能在提供的多个固定值中，任选其一**

```typescript
type 类型别名 = 固定值1 | 固定值2 | 固定值3 | 固定值4

let 变量名: 类型别名 = 四个固定值任选其一
```

2. **枚举类型：**

**概念：和字面量类型相似，也是固定值，任性其一，但是枚举类型，不仅可以当作类型使用，也可以像普通数据一样，进行取值**

**注意：枚举类型在使用时，也是单独用一个 ts 文件进行管理，新建 enums 文件夹，并新建 index.ts 文件，在此定义枚举类型**

```typescript
export enum 类型名称 {
  自定义名称 = 固定值1（不指定默认为1）,
  自定义名称 = 固定值2（不指定默认为2）,
}
```

**枚举类型，在当作类型使用时，限定了只能在提供的多个固定值中，任性其一**

**枚举类型，在当作数据使用时，使用 “ 类型名称.自定义名称 " 访问固定值数据**



##### （三）类型断言

**当获取 dom 元素时，typescript 并不能推导出准确的类型，因此需要手动指定准确的类型，这个过程被称为 “ 类型断言 ”**

```typescript
const DOM名称 = document.querySelector(通过id或者类名绑定的DOM元素)
```

**注意：该 “ DOM名称 ”，只包含公共的属性或者方法，不包含标签特有的属性**

**解决方式：使用类型断言指定更加具体的类型**

```ts
const DOM名称 = document.querySelector(通过id或者类名绑定的DOM元素) as 准确的类型
```

**尤其注意：该准确类型由于比较复杂，因此可以通过 “ document.createElement ” 手动创建一个同类型标签，然后鼠标触摸，即可获得准确的类型**



##### （四）非空断言

**当 Typescript 提示目标对象可能未定义，但是明确知道它存在，则可以使用非空断言（在目标对象后面加感叹号：目标对象!.xxx）**

**使用场景：如 find 方法寻找到的对象（保存在变量名中），对其操作时会提示该对象可能未定义，此时可以使用非空断言**



##### （五）泛型（单独抽离总结）

**泛型通常运用于 “ 函数 ” 和 “ 类型别名 ”（包括 interface 定义的接口，它是特殊的类型别名，只适用于对象）**

**（Ⅰ）函数泛型**

**当定义一个函数（方法）时，不知道该函数需要传递什么类型的参数时，而 any 又具有不安全性，因此可以使用泛型搭配函数进行使用**

```typescript
// 普通函数：函数名后面使用尖括号，定义一个泛型变量即可，在调用函数时，可接收该函数在调用时传入的类型
function 函数名<泛型变量1，泛型变量2>(参数名: 泛型变量1): 泛型变量2 { return 值 }
// 箭头函数：等号后面使用尖括号，其余相同
const 函数名 = <泛型变量1，泛型变量2>(参数名: 泛型变量1): 泛型变量2 => { return 值 }

// 调用函数时，传递想要的类型即可，如 String，表明该函数接收的参数类型为String，返回类型为Boolean
函数名<String, Boolean>("谢志强")
```

**注意：泛型变量可以进行约束，示例如下**

**<泛型变量 extends 类型别名或接口名>：这个泛型变量必须包含这个类型别名或接口名里的内容，可以有更多内容**

**<泛型变量1，泛型变量2 extends keyof 泛型变量1>：泛型变量1是一个字面量类型的对象（固定值的对象），泛型变量2的取值只能是泛型变量1的键（固定值）**



**（Ⅱ）类型别名（接口）泛型**

```typescript
// 定义一个根据泛型变量发生变化的接口类型
interface 接口名<泛型变量> {
  属性名1: 类型, 
  属性名2: 泛型变量, 
}

type 类型别名 = 定义其它类型

// 结合使用（将其它类型传递给该接口）
接口名<类型别名>
```

**注意：通常给 axios 请求返回的结果限定类型，使用接口泛型**



**（Ⅲ）TS 内置泛型方法**

1. **从某个类型中排除某些属性，并返回一个新的类型：Omit < 类型别名，' 属性名1 ' | ' 属性名2 ' >**
2. **从某个类型中拿取某些属性，并返回一个新的类型：Pick < 类型别名，' 属性名1 ' | ' 属性名2 ' >**
3. **把某个类型中的所有属性，全部转换成非必选，并返回一个新的类型：Partial < 类型别名 >**
4. **把某个类型中的所有属性，全部转换成必选，并返回一个新的类型：Required < 类型别名 >**



#### 六、Vue + TS 相关配置

##### （Ⅰ）路径配置

**使用 yarn create vite 命令构建的项目，尤其是和 Typescript 结合，在路径中是不能使用 @ 这个符号的，需要进行如下配置：**

**尤其注意：使用最新的创建项目命令（yarn create vue），创建的项目无需额外配置 @ 路径，默认可以直接使用**

1. **安装相关TS类型包（类型包一般为 @types 开头）：yarn add @types/node -D**

**尤其注意：TS类型包，在安装时，都是 -D 结尾，也就是开发依赖（非项目依赖）**

2. **在 vite.config.ts 中增加配置：**

```typescript
// 必须引入 path 方法
import path from "path";

export default defineConfig({
  其它配置...
  // 配置 @ 语法，使得 vue 项目中可以使用 @ 作为路径
  resolve: {
    alias: {
      "@": path.resolve(__dirname, "./src"),
    },
  },
});
```

3. **在 tsconfig.json 中的 compilerOptions 里增加如下配置：**

```json
{
  "compilerOptions": {
    // 此处增加如下配置
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  }
}
```

**注意：修改完 vite.config.ts 文件可能需要重启项目或 VS Code**



##### （Ⅱ）Less 和 Sass 的使用

`注意：基于 vite 创建的项目，无需额外安装 less-loader 或 sass-loader，只有基于 webpack 创建的 vue2 项目才需要安装`

**（一）使用 less，需要安装 less 开发依赖包，yarn add -D less**

**补充：新建 xxx.less 文件，可以在该文件中使用如下两种技术**

1. **Less 定义变量（场景：网站更换主题色）：@变量名: 属性值；（使用：css属性名：@变量名）**

2. **Less 使用混合注入（定义一个函数，该函数包含 css 样式复杂代码片段）：. 函数名（）{ 多种css样式 }；（使用：类名{ . 函数名（） }**

**定义好的变量或混合注入函数，在组件中使用，需要导入 less 文件（@import "文件路径";）**

**尤其注意：导入 less 文件时，末尾有分号，不可省略**



**（二）使用 sass，需要安装 sass 开发依赖包，yarn add -D sass**

**注意：sass 是 scss 的更高版本，安装完 sass 后，通常还是习惯使用 .scss 后缀的文件开发，使用的还是旧版本 scss 语法**



**（三）Vite 中全局配置 Less 或 Scss**

**进阶（自动导入 less 或 scss 文件）：在 vite.config.ts 中增加配置，相当于全局引入 less 或 scss 文件，一次导入，任意组件都可以使用**

**尤其注意：全局配置的 less 或 scss 文件，除了可以在该文件中通过类名的形式自定义全局样式，或直接设置某些样式，如滚动条样式、标签元素样式（但只针对根节点 `<div id="app"></div>` 里的内容生效，直接对 body 设置样式无效），还可以全局使用该文件中定义的变量和混入器**

**补充说明：在 main.ts 中全局引入样式文件，只能使其中的样式生效（在任意文件使用里面的类名），但里面的变量和混入器无法访问，需再次额外引入该文件**

```typescript
export default defineConfig({
  其它配置...
  css: {
    // 在 Vue2 中使用 loaderOptions 代替 preprocessorOptions 即可（注意 Vue2 是在 vue.config.js 中增加配置）
    preprocessorOptions: {
      less: {
        additionalData: `
          @import "less文件1的路径";
          @import "less文件2的路径";
        `
      },
      scss: {
        additionalData: `
          @import "scss文件1的路径";
          @import "scss文件2的路径";
        `
      }
    }
  }
})
```



##### （Ⅲ）css 兼容文件 normalize.css 文件的使用

**该文件的主要作用是，让 css 样式兼容各版本浏览器（仅仅微调样式，做兼容处理，并非样式重置）**

1. **安装 normalize.css：yarn add normalize.css**
2. **在 main.ts 中导入该文件即可：import "normalize.css"**
3. **后续导入其它一些 base.css 自定义重置样式（详见 HTML 笔记），或 common.css 公共样式（如头部或底部的公共样式）**



##### （Ⅳ）设定组件 name 相关配置

**在 Vue3 中，给组件设定组件名（name），用于在控制台快速查找到对应组件，在 script-setup 中设定 name 属性即可，但需要额外配置**

1. **下载插件：yarn add vite-plugin-vue-setup-extend -D**
2. **在 vite.config.ts 配置该插件：**

```typescript
// 必须导入该方法
import vueSetupExtend from 'vite-plugin-vue-setup-extend'

export default defineConfig({
  // 配置插件，数组第一项是配置 vue 内部插件，第二项是配置可设定组件名（name）的插件
  plugins: [vue(), vueSetupExtend()],
  其它配置...
})
```

**尤其注意：设置 name 时，首字母必须大写（规范），因为即便小写，被插件转换后的 name，首字母也是大写**

**尤其注意：script 标签里必须要有内容（可以事先写一行注释），才会使 name 生效**



##### （Ⅴ）vite 配置 mock 数据

**配置相关插件，让项目在启动时，自动运行 mock 数据服务，自定义假数据接口测试**

1. **下载 mockjs 插件（项目依赖）：yarn add mockjs**
2. **下载 vite-plugin-mock 插件（开发依赖）：yarn add vite-plugin-mock -D**
3. **在 vite.config.ts 配置该插件，并改造默认的 defineConfig 写法：**

```typescript
// 额外引入 ConfigEnv 类型和 UserConfigExport 类型（弃用 defineConfig 类型）
import { ConfigEnv, UserConfigExport, defineConfig（弃用） } from "vite"

// 引入 viteMockServe 插件
import { viteMockServe } from 'vite-plugin-mock'

// 此处进行写法改造，替换默认的 defineConfig 写法（需额外引入 ConfigEnv 类型和 UserConfigExport 类型）
export default ({ command, mode }: ConfigEnv): UserConfigExport => {
  // 将所有配置，以对象的形式 return 出去
  return {
    // 配置插件，数组第一项是配置 vue 内部插件，第二项是配置 mock 自启服务的插件
    plugins: [
      vue(),
      viteMockServe({
        // 由于 mock 文件夹路径是在根目录，因此此处路径直接是 mock
        mockPath: 'mock',
        // command 是项目环境（开发 serve 或者生产 build），mode 是模式（development 或者 production）
        enable: command === 'serve',
        // 生成大量文件的 BUG，临时解决方案（如无BUG可移除该配置）
        watchFiles: false
      })
    ],
    其它配置...
  }
}
```

4. **在项目根目录处，新建 mock 文件夹（需要额外配置使 ts 能识别），并在该文件夹下新建任意 xxx.ts 文件，自定义接口的规范如下：**

```typescript
// 额外引入 mock 类型
import { type MockMethod } from 'vite-plugin-mock'

// 引入 mock，并解构出相关工具函数 Random
import mock from 'mockjs'
const { Random } = mock

export default [
  {
    url: '/自定义接口地址（不含根地址）',
    method: 'get（请求方式）',
    response: ({ query }) => {
      // 可利用 query 拿到传递过来的数据（都包含在 query 对象中）做一些事情
      return {
        // 与后端规定好返回数据的通用格式
        code（自定义）: 200,
        message（自定义）: "获取数据成功",
        data（自定义）: {
          自定义名称: "自定义返回的结果",
          // 通过 Random. 的方式可自动提示相关方法，注意要加括号
          自定义名称: Random.生成随机数据的方法(),
          // 模拟数组数据，里面每个对象的内容自定义
          自定义名称: new Array(20).fill(undefined).map((item, index) => {
            return {
              id: Random.id(),
              title: Random.ctitle()
            }
          })
        }
      }
    }
  }
] as MockMethod[]
```

5. **测试接口：在浏览器地址栏，紧接后面写 `/自定义接口地址?参数名=参数值&参数名=参数值`，回车即可（注意参数值无引号）**
6. **mock 假接口的根地址，是本地项目启动的 localhost 地址，实际开发中需要配置反向代理到该地址（触发关键词是 ”/mock“，不需要开启跨域)**



##### （Ⅵ）vite 配置跨域代理

**在 vite.config.ts 中增加配置：**

```typescript
export default defineConfig({
  其它配置...
  server: {
    proxy: {
      // 开发环境代理
      '/api': {
        target: '开发环境的请求地址',
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, '')
      },
      // mock 代理
      '/mock': {
        target: '本地项目启动的 localhost 地址',
        // changeOrigin: true,
        rewrite: (path) => path.replace(/^\/mock/, '')
      }
    }
  }
});
```



#### 七、Vue3 与 Typescript 的结合使用

**Typescript 中有一种类型的应用，在 Vue 中特别常见，被称之为 “ 泛型 ”，也称作 “ 泛型函数 ”，主要应用于函数**

**所谓 “ 泛型 ”，就是不确定的类型，在函数名和括号的中间使用尖括号，包裹多个泛型，该泛型用于接收函数调用时真正传递过来的类型，然后给函数的参数和返回值设定该泛型，当调用函数时，在同样位置通过尖括号传递真正想要的类型，可以实现在调用函数时，根据传递的类型不同，该函数的参数和返回值类型也不同**

```typescript
// 定义函数的同时使用泛型
function 函数名<泛型1，泛型2>(参数1: 泛型1, 参数2: 泛型1): 泛型2 {
  return 结果
}

// 调用函数，传递想要的类型
函数名<想要的类型1，想要的类型2>（参数1，参数2）
```





##### （一）Typescript 中 defineProps 的使用

**父传子，接收时的写法改变，将 props 接收的变量（父组件传递的自定义变量名，也称自定义接受名），限定类型，再配合泛型，传递给 defineProps 函数内部**

```typescript
const props 或 { 自定义接收名1, 自定义接收名2 = '默认值' } = defineProps<{
  // 必传
  自定义接收名1: 类型
  // 非必传
  自定义接收名2?: 类型
}>()
```

**注意：尖括号里的内容，可以提取出来，并使用 type 单独设定类型别名，一般为 PropsType，然后将 PropsType 写在尖括号内**

```typescript
// 定义 props 接收的类型
type PropsType = {
  // 必传
  自定义接收名1: 类型
  // 非必传
  自定义接收名2?: 类型
}

// 通过泛型传递该类型
const props 或 { 自定义接收名1, 自定义接收名2 = '默认值' } = defineProps<PropsType>()
```

**必传项和默认值的使用：**

1. **必传项和非必传项，使用 " ? "（问号）的方式代替**

2. **通过解构并赋值的方式设定默认值，若想要解构出来的自定义接收名，仍然具有响应式，且可以使用默认值，需要在 vite.config.ts 文件中另外配置**

   **注意：不配置无法设置默认值，且无响应式（一定要设置）**

```typescript
export default {
  plugins: [
    vue({
      reactivityTransform: true
    })
  ]
}
```



##### （二）Typescript 中 defineEmits 的使用

**子传父，提前声明子组件向父组件提交的自定义事件，并获取 emit 方法，写法有所变化**

```typescript
const emit = defineEmits<{
  // 传递的数据是个变量，需要给该变量限定类型
  (e: '自定义事件1', 需要传递的数据（变量）: 类型): void
  // 传递的数据是个固定值，需要给 value 限定类型（固定写法）
  (e: '自定义事件2', value（固定值）: 类型): void
}>()
```



##### （三）Typescript 中 ref 的使用

**实际开发中，无论是简单数据，还是复杂数据，都使用 ref 进行包裹，如果是简单数据，正常写即可，如果是复杂数据，必须配合泛型，指定类型**

```typescript
// 定义一个对象数组类型
type 类型别名 = {
  属性名1: 类型
  属性名2: 类型
}[]
// 使用泛型传递该类型别名
const list = ref<类型别名>([])
```

**尤其注意：当传递的是对象数组类型，初始值可以为空数组，不会报错（原因：对象数组包含空数组，所以空数组符合对象数组类型）**

**尤其注意：如果 ref 包裹的初始值为 null（一般只有在使用 ref 绑定组件时，默认值才设为 null），声明类型时应该具有 null 类型**



##### （四）Typescript 中利用 ref 绑定元素或组件

1. **定义一个变量（用于绑定 DOM 或者组件）：**

```typescript
const 名称变量（规范：DOM名称或者组件名称+Ref） =  ref<null | 类型断言>（null）
```

**注意：当使用 ref 绑定 DOM 元素或组件后，需要使用 ” 类型断言 “ 准确的告诉 ” 名称变量.value "（注意：ref 后面的尖括号里的类型，是对 ” 名称变量.value " 进行声明，而非名称变量）应当是什么类型，如果 ref 绑定的是 DOM 元素，通过 “ document.createElement ” 手动创建一个与 ref 绑定元素同类型的标签，然后鼠标触摸，可获得 DOM 元素的准确类型，如果 ref 绑定的是组件，直接使用鼠标触摸该组件，即可获得该组件的准确类型**

2. **给标签或者组件添加属性：ref = “ 名称变量 ”**

**尤其注意：当操作 ref 绑定的 DOM 元素（名称变量.value）中的属性时，Typescript 可能会提示 ref 目标可能为 null 的错误（也就是 “ 名称变量.value ” 可能为 null，原因是，在之前定义名称变量的时候，对 “ 名称变量.value ” 声明了两个类型，可以为准确的类型，也可以为 null，而 null 是不可以使用属性的），此时使用非空断言（感叹号）即可，告诉 Typescript 此时不为 null（不为空）**



##### （五）类型声明在项目中的实际运用

**`多搭配类型声明文件使用，所谓类型声明文件，就是 xxx.d.ts 文件`**

**`尤其注意：xxx.d.ts 文件的名称，可任意命名，在配置范围内，都可以被 TS 识别（TS 默认识别 src 文件夹下的文件），且被识别后的类型声明文件，会被作为全局文件，在整个项目中全局生效（无需导出、导入，即可使用），如果在 src 文件夹以外新建类型声明文件，需要在 tsconfig.json 文件中额外配置，才能让 TS 识别`**

```typescript
{
  "compilerOptions": {
  },
  // 与 compilerOptions 同级处的 include 中，配置路径，让 TS 可以进行识别（TS 生效范围）
  "include": [默认配置（基本都是对src文件夹下的文件进行识别）, "新建文件夹/**/*.文件后缀名（此处为 d.ts，根据文件不同后缀名也不同)"],
}
```

**`尤其注意（极其重要）：此方法同样适用于，任何自定义的文件夹下的任意后缀文件（vue 文件中也存在 TS 语法，需要被识别），只要该文件使用到了 TS，且需要 TS 的类型提示，却无法被 TS 识别（例如：非 src 下的 .ts 文件，没有 vue 内置的 TS 类型提示），只需配置文件路径，让该文件被识别即可`**

**（Ⅰ）正常的类型声明后，可将自己写的类型声明，单独抽离出去，且写成类型别名的形式（或 interface 接口别名），其它变量或方法可复用**

**`此种情况适用于自己写的变量或方法，单独在类型声明文件中，定义类型，并以类型别名（或接口别名）的方式按需导出，给自己写的变量或方法使用对应的类型别名`**

1. **在 src 下新建 types 文件夹（TS 默认识别 src 文件夹下的文件，且全局生效），并在里面新建多个 “ xxx.d.ts ” 文件（名称不同，模块化区分）**
2. **在 “ xxx.d.ts ” 文件中，按需导出多个类型声明（正常使用 type 声明类型，前面加上 export 即可）**
3. **在需要使用类型声明的 vue 组件中，按需导入类型别名即可（ import { 类型别名1，类型别名2 } from ’ . / type / xxx ‘ ）**

**注意：如果类型声明文件是全局生效的（通过配置使其被 ts 识别），则不需要按需导出和按需导入，任意 vue 组件中（或其它文件）都可直接访问到里面的类型（不安全，可能有多个类型文件是全局生效，但里面的类型别名相同而导致冲突）**

**注意：按需导入某个文件中的类型别名时，该文件名的 “ .d.ts ” 后缀，可以直接省略**

**尤其注意：自己声明类型并复用的情况下，不要求必须在 src 下新建 types 文件夹，在其它位置也可以，并且不需要额外配置文件路径，使 TS 识别并全局生效，因为自己声明类型并复用的情况下，通常会使用模块化导出和导入对应类型，换而言之，如果利用模块化使用类型，便无需考虑 TS 识别问题**



**（Ⅱ）针对项目中已存在、且不可更改的 js 文件（不是自己写的，不建议在文件中直接声明），进行类型声明**

**当遇到项目中已存在的 js 文件，由于该文件不可随意更改（在文件中直接声明类型，可能会对项目中其它地方造成影响），因此，可以在该 js 文件的同级目录处新建一个同名的 .d.ts 文件（并非必须同名，只是规范），当在组件中导入该 js 文件并暴露相关变量时，TS 会自动加载与 js 文件同名的 .d.ts 文件（本质是该位置默认被 TS 识别，使其全局生效，若自定义 .d.ts 文件的位置，则必须额外配置文件路径使其被 TS 识别，因为这不是模块化，配置是必要的），以提供类型声明，在这个  .d.ts 文件，对所有变量或者方法进行类型定义（注意：该定义形式，与正常的 type 声明类型不同，不仅仅只是声明类型，还要与相关变量和方法产生联系，需要完整的包含 ” 变量名和方法名：类型 “，这样在组件中使用 js 文件的变量和方法时，可以直接使用，无需额外操作）**

1. **原始 js 文件（不是自己写的，不能随意更改，因此不能直接在里面进行声明）：**

```javascript
let count = 10
let songName = '谢志强'
let position = {
  x: 0,
  y: 0
}

function add(x, y) {
  return x + y
}

function changeDirection(direction) {
  console.log(direction)
}

const fomartPoint = point => {
  console.log('当前坐标：', point)
}

export { count, songName, position, add, changeDirection, fomartPoint }
```

2. **同级目录下，新建同名 .d.ts 文件（若原始 js 文件没有被单独的文件夹包裹，只需要遵循在原始 js 文件同级目录处新建即可）：**

```typescript
declare let count:number

declare let songName: string

interface Position {
  x: number,
  y: number
}

declare let position: Position

declare function add (x :number, y: number) : number

type Direction = 'left' | 'right' | 'top' | 'bottom'

declare function changeDirection (direction: Direction): void

type FomartPoint = (point: Position) => void

declare const fomartPoint: FomartPoint

export {
  count, songName, position, add, changeDirection, FomartPoint, fomartPoint
}
```



**（Ⅲ）对 .env 配置文件中的变量，进行类型声明**

**当使用 import.meta.env 可以读取到项目环境中默认的多个变量，尤其注意，import.meta.env 的值为对象的形式**

**当需要自定义环境变量时，需要在项目根目录新建 .env 文件，自定义变量，必须是 `键=值` 的形式，且键名必须以 “ VITE_ " 开头，但自定义的环境变量，在输入 `import.meta.env.` 时，不会触发自定义环境变量的建议，因此需要为 import.meta.env 最终的结果（对象形式）提供类型声明**

**为 import.meta.env 提供类型声明：**

1. **在 src 文件夹下新建 env.d.ts 文件（类型声明文件的名称可任意，此处只是为了保持同名规范），并声明类型**

**注意：若该类型声明文件新建在 src 文件夹以外，需额外配置 TS 的生效范围**

```typescript
interface ImportMetaEnv {
  readonly 自定义环境变量名: string;
  // 更多环境变量...
}

interface ImportMeta {
  readonly env: ImportMetaEnv;
}
```

2. **此时输入 `import.meta.env.` 时，会触发 TS 类型提示和建议**



##### （六）Typescript 中 pinia 的使用

**state 中的属性名，非表达式的形式（属性名 = 值），而是键值对形式（属性名：值），因此无法在属性名后面直接声明类型，而是必须使用 “ as ” 关键字进行类型断言（pinia 中对 state 中的属性进行类型声明，统一使用类型断言）**

```typescript
state里的属性名: [] as 类型断言（此处为对象数组类型，且对象数组类型包含空数组，因此初始值为空数组不会报错）
```



##### （七）Typescript 中 Axios 的使用

1. **在 src 下的 types 文件夹中，新建一个 data.d.ts 文件（规范：该文件用于保存所有请求返回数据的类型）**

```typescript
// 根据不同项目规定好的请求接口，所返回的结果规范，将相同部分抽离出来，提前定义一个请求接口返回结果的通用类型，真正需要的数据用泛型变量代替
export interface ApiRes<T> {
  code: string
  message: string
  result: T
}

// 单个请求接口返回结果的类型（result 部分）
export type 类型名称 = {
  id: string
  name: string
  picture: string
}
```

2. **按照旧版本的封装方法，封装单个的请求方法时，对其中最原始的 request 部分进行类型声明即可**

```typescript
// 以下情况仅适用于：未对响应拦截器最终返回结果进行处理，将完整的请求接口所返回的结果类型传递给第一个泛型参数即可，第二个泛型参数省略
request.get< ApiRes<类型名称[]> >(请求地址和参数等)
request.post< ApiRes<类型名称[]> >(请求地址和参数等)
```

3. **进阶封装方法：在 src/utils 目录下新建 request.ts 文件中，除了基础封装（请求拦截，响应拦截），继续定义一个请求工具函数，最后导出该工具函数即可**

```typescript
// 引入使用仓库的通用方法
import useStore from '@/stores'

// 引入路由（注意是引入路由文件夹下根文件默认导出的路由实例），当后续请求响应失败时（401报错，token过期），通过路由实例提供的跳转方法强制跳转登录页面
import router from '@/router'

// 引入 axios，并引入请求方法的类型
import axios, { type Method } from 'axios'

// 引入elementPlus的消息提示方法
import { ElMessage } from 'element-plus'

// 尤其注意，不能在此处使用仓库（在仓库中调用 axios 方法，会走此处代码，此时还未初始化完成 pinia 仓库）
// const { user } = useStore()

// 由于最终封装好的工具函数名称为 request，因此前面的基础封装使用 instance 来命名
const instance = axios.create({
  baseURL: '基地址',
  timeout: 超时时间
})

// 请求拦截器，携带 token
instance.interceptors.request.use(
  (config) => {
    // 拿到 user 仓库，稍后注入 token
    const { user } = useStore()
    // 如果 token 存在，才注入 token（避免 token 不存在，执行后报错）
    if (user.token && config.headers) {
      config.headers['Authorization'] = `Bearer ${user.token}`
    }
    return config
  },
  (err) => Promise.reject(err)
)

// 响应拦截器，剥离无效数据，进行无感刷新，或有感刷新
instance.interceptors.response.use(
  (res) => {
    const { 后端自定义的请求状态, 消息提示 } = res.data
    // 虽然响应成功，但是后端返回的是失败状态，代表业务逻辑失败，但由于响应是成功的，需要强制让它不成功
    if (后端自定义的请求状态是失败状态) {
      // 提示错误消息，强制 reject 抛出错误
      ElMessage({
        message: 消息提示 || '业务失败',
        type: 'error'
      })
      return Promise.reject(new Error(消息提示))
    }
    // 业务逻辑成功，返回响应数据，作为 axios 成功的结果（此处的响应拦截，对响应返回的结果只需剥离一层即可，即返回 res.data 即可）
    return res.data
  },
  (err) => {
    // 拿到 user 仓库，稍后调用其中的方法，对 token 失效进行处理
    const { user } = useStore()
    // 当响应拦截器响应失败，也提示错误消息
    ElMessage({
      message: err.message,
      type: 'error'
    })
    // 后端对401报错（token过期）做了处理，会直接使响应拦截器响应失败
    if (err.response.status === 401) {
      // 删除用户token及用户信息
      user.删除用户token的方法()
      user.删除用户信息的方法()
      // 跳转登录页面，并且带上接口失效所在页面的地址，登录完成后回跳使用
      router.push({
        path: '/login',
        query: { returnUrl: router.currentRoute.value.fullPath }
      })
    }
    return Promise.reject(err)
  }
)

// 根据不同项目规定好的请求接口，所返回的结果规范，将相同部分抽离出来，提前定义一个请求接口返回结果的通用类型，返回数据中，真正需要的部分用泛型变量代替
type ApiRes<T> = {
  code: string
  message: string
  result: T
}

// 定义一个请求工具函数（Method 类型需要从 axios 中引入）
const request = <T>(url: string, method: Method = 'get', submitData?: object) => {
  // axios 底层方法，定义了两个泛型参数，其中第二个参数，需要传递的类型，就是响应拦截器最终处理并返回的数据类型（两者保持一致）
  // 由于此处响应拦截返回的最终数据就是 res.data，因此需要传递完整的数据类型给第二个泛型参数，也就是 ApiRes<T>
  return instance.request<T, ApiRes<T>>({
    url,
    method,
    [method.toLowerCase() === 'get' ? 'params' : 'data']: submitData
  })
}

export default request
```



##### （八）Typescript 中定时器的使用

1. **定时器名称，一定是 Number 类型，且一定是 let 声明，且不需要使用 ref 包装，且推荐值为 -1**

```typescript
let 定时器名称: number = -1;
```

2. **在 onMounted 钩子函数中书写定时器，由于 Vue 单文件的 script-setup 属于模块，当处于 Typescript 环境，编辑器会识别成 node 中的定时器，需要使用 window 对定时器进行修饰，使其被编辑器正确识别**

```typescript
onMounted(() => {
  // 建议每次使用定时器时，首先清除定时器，确保只开启了一个定时器（良好的书写习惯）
  clearInterval(定时器名称);
  // 使用 window 对定时器进行修饰，如果毫秒数可定制，毫秒数是 number 类型
  定时器名称 = window.setInterval(() => {
      处理程序
  }, 毫秒数);
});
```

3. **在 onBeforeUnmount 钩子函数中清除定时器**

```typescript
onBeforeUnmount(() => {
  // 清除定时器
  clearInterval(定时器名称);
});
```



##### （九）Typescript 中事件函数的 event 对象

1. **event 的类型绝大多数情况是 Event，具体类型可在绑定事件处，通过鼠标触摸 $event 获取，声明该类型后，可以通过 event. 的形式，获得 TS 提示，使用 event 的相关方法（如阻止冒泡、阻止默认行为）**
2. **但是 event.target  获取的是当前事件具体点击的元素（event.currentTarget 获取的是当前事件绑定的元素，此处以 event.target 举例），TS 不知道它的类型，因此需要提前观察是什么元素，再使用创建元素的形式获取该元素类型**
3. **使用类型断言，给 event.target 声明类型：event.target as 类型**
4. **通过 (event.target as 类型). 的形式，获得 TS 提示，使用相关方法（如 value）：(event.target as 类型).value**



#### 八、Vue 中实现导航栏菜单，鼠标移入悬浮弹窗

**布局：使用 ul li 布局导航栏菜单，li 中除了 a标签（菜单项），还应该具有一个 div（对应的悬浮弹窗），这样给 li 设置 hover 属性，当里面的悬浮弹窗显示后，鼠标移入悬浮弹窗，仍然处于  li 的范围，悬浮弹窗不会消失**

**悬浮弹窗的处理：绝对定位调整位置，通过样式控制，使其默认不显示（高度为 0，注意设置 overflow: hidden，视情况选择是否设置 opacity 为 0），此处的处理，是对所有的悬浮弹窗进行处理，即不显示，当鼠标悬停在 li 上，再单独显示它自己的悬浮弹窗（有高度，opacity 为 1）**

**解释：为什么鼠标悬停到 li 上，显示的不是所有悬浮弹窗，而是自己的悬浮弹窗，因为 `li:hover div` 选择器选中的是当前 hover 元素下的 div，而非所有 div**

**数据渲染：导航栏菜单的数据正常情况是一次性返回，其中每个对象都包含当前菜单项及对应的子数据，循环渲染即可**

**使用 hover 可能造成的问题：**

**无论是 Vue2 还是 Vue3 中，当一个路由导航通过 hover 展示悬浮弹窗（非简单文字颜色变化），当点击该路由导航后，即便路由进行了跳转，由于 vue 是单页面应用，路由跳转并不会刷新页面，因此 hover 一直处于触发状态，并没有关闭，则会造成悬浮弹窗仍然存在，造成遮挡等影响，可采取以下两种解决办法**

**（一）暴力解决（此处以 Vue3 示例）**

1. **提前导入 router 方法：**

```javascript
import { useRouter } from "vue-router";
const router = useRouter();
```

2. **给路由导航添加点击事件，强制刷新页面：**

```html
< RouterLink to="路径"  @click=“ router.go(0) ”>导航名称< /RouterLink >
```



**（二）改造 hover**

1. **将 hover 后的悬浮弹窗样式部分（仅这一部分，其它 hover 简单效果保留）改造成类名形式**

2. **由于多个路由导航（包括每个路由导航对应的悬浮弹窗），都是通过数据渲染而来，因此可以通过动态类名来决定是否显示悬浮弹窗**
3. **改造数据（一般数据是请求回来，并保存在一个变量中，因此可以直接对请求回来的结果进行改造，然后保存在变量中），通过 foreach 方法，为数据（数组形式）里面的每一项（对象形式）添加一个 open 属性，默认值为 false，该 open 属性就是动态类名是否显示悬浮弹窗的判断条件**
4. **给路由导航添加鼠标移入移出事件（mouseenter 和 mouseleave），当移入鼠标时，通过 find 方法，找到当前路由导航对应的数据（对象形式，可直接修改对象内部的数据），并将当前数据的 open 属性值 改为 true，移出同理**
5. **给路由导航添加点击事件，当点击该路由导航，将当前路由导航对应数据中的 open 属性值 改为 false，对应的悬浮弹窗同理**



#### 九、vueuse/core 的使用

**概念：vueuse/core 是一个封装了常见交互逻辑的包**

1. **安装包：yarn add @vueuse/core**
2. **参考 vueuse 官方文档使用即可**

**（一）实时获取页面整体向上滚动（body 里的内容向上滚动）的距离（可实现吸顶效果等）**

**尤其注意：该方法监听的是 body 里面内容的滚动，要求当前产生滚动的元素必须是 body（注意：body 默认具有滚动条），且 body 里面没有其它具有滚动条的元素进行干扰（可能导致当前滚动元素非 body 里的内容滚动），方可使用（可利用控制台代码快速找到当前是哪个元素具有滚动条，从而内部可以滚动）**

```javascript
import { useWindowScroll } from '@vueuse/core'
// 该 y 值实时变化，获取到的是页面整体向上滚动的距离，除此之外还有 x，获取的是页面整体向左移动的距离
const { y } = useWindowScroll()
```

**（二）实时获取自定元素里面内容整体向上滚动的距离（可实现吸顶效果等）**

**尤其注意：该方法监听的是自定元素里面内容的滚动，要求该自定元素必须具有滚动条（即里面的内容可滚动），方可使用，若想要该自动元素具有滚动条，可为其设置固定高度，并使用 overflow-y: auto，即可让该元素具有滚动条**

```javascript
// 前提条件：给自定元素（也就是外层容器）绑定 ref，调用 useScroll 方法时，传入该外层容器的 ref 名称即可
import { useScroll } from '@vueuse/core'
const 外层容器ref名称 = ref<元素类型 | null>(null)
// 该 y 值实时变化，获取到的是该自定元素里面内容整体向上滚动的距离，除此之外还有 arrivedState 对象，具有上下左右四个属性，到达各自边界时，为 true
const { x, y, arrivedState } = useScroll(外层容器ref名称)
```

**（三）全屏显示效果（同样适用于网页中的视频，实现全屏播放）**

```javascript
import { useFullscreen } from '@vueuse/core'
// isFullscreen 是实时变化的状态标识，用于判断当前是否全屏，toggle 是切换到全屏或者退出全屏的方法（合适的时候调用即可）
const { isFullscreen, toggle } = useFullscreen()
```

**（四）当屏幕尺寸发生变化，实时获取屏幕的宽度（某些组件库的组件要求传递宽度，且不能写死，考虑到移动适配，需要根据屏幕宽度实时计算）**

```javascript
import { useWindowSize } from '@vueuse/core'
// 该 width 的值，随着屏幕尺寸变化实时变化，除此之外还有 height，获取的是屏幕的高度
const { width } = useWindowSize()
```

**（五）利用 cookie 进行存储，移除数据等（cookie 的逻辑较为复杂，可以使用该工具）**

**需要额外安装依赖：npm i universal-cookie**

**需要额外安装依赖：npm i @vueuse/integrations**

```javascript
import { useCookies } from '@vueuse/integrations/useCookies'
// 其中 cookie 对象中包含 get、set、remove 等方法
const cookies = useCookies()

// 使用方式（以 set 举例）
cookies.set('键名', 保存的值)
```

**（六）监听某个元素（容器）尺寸的变化（某个容器大小发生改变，使其中的 echarts 图表重新渲染，即调用 " echarts 实例.resize() " 方法）**

```javascript
import { useResizeObserver } from '@vueuse/core'

// 给某个元素（容器）绑定 ref 名称
useResizeObserver(容器的ref名称, (entries) => {
  const entry = entries[0]
  // 容器实时变化的宽高
  const { width, height } = entry.contentRect
})
```

**（七）实时监听鼠标相对于整个页面的位置，也可监听鼠标相对于目标元素内的位置，以及鼠标是否在目标元素范围内**

```javascript
// 前提条件：给目标元素绑定 ref，调用 useMouseInElement 方法时，传入该目标元素的 ref 名称即可
import { useMouseInElement } from '@vueuse/core'
const 目标元素ref名称 = ref<元素类型 | null>(null)
// 以下值实时变化，x 和 y 是鼠标相对于整个页面的位置，elementX 和 elementY 是鼠标相对于目标元素的位置，isOutside 判断鼠标是否在目标元素范围内
const { x, y, elementX, elementY, isOutside } = useMouseInElement(目标元素ref名称)
```



#### 十、深度作用选择器在 Vue3 中的使用

**深度作用选择器用于设置当前组件中子组件的 css 样式，在 Vue3 中 废弃了 “ /deep/ ” 和 “ ::v-deep ”，使用 “ :deep ( css选择器 ) { css样式 } ” 替代**

**或者使用 ::v-deep() { 组件库内置类名: { css 样式 } }，或者使用 ::v-deep { 组件库内置类名: { css 样式 } }**



#### 十一、骨架屏的使用

**概念：当数据请求回来之前，自定义一个盒子进行占位，通过 v-if 和 v-else 判断有无数据，来控制是否显示骨架屏（当数据请求回来后，渲染真实效果）**

**以下骨架屏组件作为参考，一般注册为全局组件，使用方式：< Skeleton :width="宽度" :height="高度" animated fade bg="颜色" />**

```html
<!-- 可复用的骨架屏组件 -->
<script lang="ts" setup name="Skeleton">
defineProps({
  // 自定义背景颜色
  bg: {
    type: String,
    default: "#efefef",
  },
  // 自定义宽度
  width: {
    type: Number,
    required: true,
  },
  // 自定义高度
  height: {
    type: Number,
    required: true,
  },
  // 是否显示动画效果
  animated: {
    type: Boolean,
    default: false,
  },
  // 是否背景色和透明色交替变化
  fade: {
    type: Boolean,
    default: false,
  },
});
</script>
<template>
  <div
    class="skeleton"
    :style="{ width: width + 'px', height: height + 'px' }"
    :class="{ shan: animated, fade: fade }"
  >
    <div class="block" :style="{ backgroundColor: bg }"></div>
  </div>
</template>

<style scoped lang="less">
.skeleton {
  display: inline-block;
  position: relative;
  overflow: hidden;
  vertical-align: middle;
  .block {
    width: 100%;
    height: 100%;
    border-radius: 2px;
  }
}
.shan {
  &::after {
    content: "";
    position: absolute;
    animation: shan 1.5s ease 0s infinite;
    top: 0;
    width: 50%;
    height: 100%;
    background: linear-gradient(
      to left,
      rgba(255, 255, 255, 0) 0,
      rgba(255, 255, 255, 0.3) 50%,
      rgba(255, 255, 255, 0) 100%
    );
    transform: skewX(-45deg);
  }
}
@keyframes shan {
  0% {
    left: -100%;
  }
  100% {
    left: 120%;
  }
}

.fade {
  animation: fade 1s linear infinite alternate;
}
@keyframes fade {
  from {
    opacity: 0.2;
  }
  to {
    opacity: 1;
  }
}
</style>

```



#### 十二、图片懒加载优化

**利用自定义指令实现图片懒加载功能**

1. **安装包：yarn add @vueuse/core**
2. **在 src/directive 文件夹下的 index.ts 文件中，导入 useIntersectionObserver 方法，该方法可以监听某个 DOM 元素或组件是否在可视范围之内**

```typescript
// 如果是 Typescript 需要引入 App，用于给 install 里的 app 指定类型（JavaScript 中可省略）
import { App } from "vue";
// 引入 useIntersectionObserver 方法
import { useIntersectionObserver } from "@vueuse/core";
// 引入默认图片，当图片地址出现错误引起撕裂时，可替换成默认图片（模块化中，静态资源资源需要引入才可使用）
import defaultImg from "@/assets/images/默认图片.png";

export default {
  // Typescript 中需要给 app 指定类型（JavaScript 中可省略）
  install(app: App) {
    app.directive("lazyImages", {
      mounted(el, options) {
        // 接收并解构出 stop 方法，是为了让监听只执行一次（当处于可视范围之内，图片加载，无需重复监听，避免再次加载图片的步骤）
        const { stop } = useIntersectionObserver(el, ([{ isIntersecting }]) => {
          // el 是目标容器（即绑定的 DOM 元素或组件，在自定义指令直接使用 el 即可，在其余地方需要配合 ref 绑定容器进行使用）
          if (isIntersecting) {
            // 当 img 元素处于可视范围之内，isIntersecting 会自动变为 true，执行如下代码，将 img 元素的 src 赋值为真实地址
            el.src = options.value;
            // 调用 stop 方法，停止监听
            stop();
            // 当图片加载错误，显示默认图片
            el.onerror = () => {
              el.src = defaultImg;
            };
          }
        });
      },
    });
  },
};
```

3. **给 img 标签使用自定义指令：< img v-lazyImages="图片地址" />**



#### 十三、数据懒加载优化

**当父组件加载完成后，无论是可视范围内，还是非可视范围内，所有子组件都会被加载，且都会在初始化时发送请求获取数据，性能消耗较大，因此可以使得组件在可视范围内再发送请求，提升性能**

**原理：使用 useIntersectionObserver 方法监听组件（通过 ref 绑定组件），当在可视范围内，发送请求**

1. **安装包：yarn add @vueuse/core**
2. **在 src/utils 文件夹下的 hooks.ts 文件中，导入 useIntersectionObserver 方法，该方法可以监听某个 DOM 元素或组件是否在可视范围之内**

```typescript
// 引入 useIntersectionObserver 方法
import { useIntersectionObserver } from "@vueuse/core";
// 通过 ref 绑定组件，需提前引入 ref 方法
import { ref } from "vue";

// 封装通用的数据懒加载方法（任意地方都可以使用），该方法接收的形参 apiFn 是一个请求方法（回调函数）
export function useLazyData(apiFn: () => void) {
  // 方法内部需要通过 ref 创建名称变量，并将这个 target 暴露出去给需要数据懒加载的组件进行绑定（后续会 return 这个 target，调用该方法时接收即可）
  const target = ref(null);
  // 接收并解构出 stop 方法，是为了让监听只执行一次
  const { stop } = useIntersectionObserver(
    // target 是目标容器（即绑定的 DOM 元素或组件）
    target,
    ([{ isIntersecting }]) => {
      // 当组件处于可视范围之内，isIntersecting 会自动变为 true，在此处可根据 isIntersecting 来判断，执行某些操作
      if (isIntersecting) {
        // 发送请求
        apiFn();
        // 调用 stop 方法，停止监听
        stop();
      }
    }
  );
  // 至关重要的一步，将名称变量暴露出去，尤其注意，在调用该方法时进行接收，最后在需要数据懒加载的组件上，添加属性 ref="target" 进行绑定
  return target;
}
```

3. **在需要数据懒加载的组件上，添加属性 ref="target" 进行绑定**

```html
<组件标签名 ref="target"></组件标签名>
```

4. **调用封装好的 useLazyData 方法，并接收内部暴露出来的名称变量，取名为 target**

```typescript
const target = useLazyData(请求方法);
```

**尤其重要（封装思想）：target 需要暴露出去的原因，是由于函数内部为局部作用域，外部无法直接访问，需要利用 return 进行暴露，因此在 Vue3 的思想中，将某个功能的一整块代码块进行抽离，丢进一个函数中进行封装，并将所有与外部有关联的变量（即外部需要使用的变量，通常情况是全部）统一包装成对象的形式 return 出去，最后调用该封装的函数时，使用解构的方式接收暴露出去的变量即可，这样在执行该封装的函数时，既执行了函数内部逻辑，且函数内部的变量，也被当前调用该函数的组件所访问并使用**



#### 十四、vee-validate 的使用（表单校验插件）

**适用于非组件库形式的表单，脱离组件库自带的校验方法，仍然可以实现表单的校验**

1. **安装相关依赖：yarn add vee-validate @vee-validate/rules yup @vee-validate/i18n**

**注意：vee-validate 是校验插件，@vee-validate/rules 是 vee-validate 对应的原生校验规则包，@vee-validate/i18n 是语言配置，yup 是增强的规则包**

2. **在 src 下新建 plugins 文件夹（规范：对插件进行封装的文件，存放在里面），并在里面新建 validate 文件夹，其中包含两个文件：yup.ts 和 index.ts**

**（Ⅰ）yup.ts 文件：**

```typescript
import * as yup from "yup";

// 自定义中文提示信息
yup.setLocale({
  // 自定类型
  mixed: {
    // 需要注意，不能为模板字符串，只能是字符串（固定写法）
    required: "${label}不能为空",
  },
  // 必须为字符串类型
  string: {
    // 外部在定义规则时，调用 lable(参数) 时，会将参数传递到 ${label}
    email: "${label}格式错误",
    // 同理，这里的 ${min} 和 ${max} 也是接收的 min 方法和 max 方法传递过来的参数
    min: "${label}长度最少为 ${min}",
    max: "${label}长度最多为 ${max}",
  },
  // 必须为数值类型
  number: {
    min: "${label}的数字不能小于 ${min} ",
    max: "${label}的数字超过了 ${max} ",
  },
});

export default yup;
```

**（Ⅱ）index.ts 文件：**

```typescript
// 由于该包内部的方法只进行了按需导出，因此将 vee-validate 中的所有方法包装成对象的形式，以 veeValidate 命名并导入
import * as veeValidate from "vee-validate";
// 导入原生的 vee-validate 规则包（@vee-validate/rules）中所有的规则方法
import rules from "@vee-validate/rules";
// 导入增强版的 yup 规则
import yup from "./yup";
// localize 方法用于使某个语言包生效，setLocale 方法用于切换某个语言配置
import { localize, setLocale } from "@vee-validate/i18n";

// 导入多个语言包（中文、英文）
import zh_CN from "@vee-validate/i18n/dist/locale/zh_CN.json";
import en from "@vee-validate/i18n/dist/locale/en.json";

// 批量定义 vee-validate 对应的 @vee-validate/rules 原生规则
Object.keys(rules).forEach((key) => {
  veeValidate.defineRule(key, rules[key]);
});

// 使多个语言包生效（中文、英文）
localize({ zh_CN });
localize({ en });

// 对 veeValidate 进行相关配置（利用 veeValidate 对象中的 configure 方法）
veeValidate.configure({
  // 语言详细配置
  generateMessage: localize({
    // 中文配置
    zh_CN: {
      // names 中的属性是 Field 和 ErrorMessage 组件对应的 name 名（对 name 重命名，更改默认提示的前缀名）
      names: {
        // 默认提示是由组件对应的 name 名（英文）+中文组成的，因此需要将 name 名重命名
        account: "账号",
        password: "密码",
      },
      // messages 中的属性是对不同规则的错误提示进行自定义（没有自定义的规则使用默认提示）
      messages: {
        // 原生规则包中固定的规则名
        required: "中文的自定义错误提示1",
        email: "中文的自定义错误提示2",
      },
    },
    // 英文配置
    en: {
      names: {
        account: "account",
        password: "password",
      },
      messages: {
        required: "英文的自定义错误提示1",
        email: "英文的自定义错误提示2",
      },
    },
  }),

  // input内容发生改变时进行校验
  validateOnInput: true,
});

// 切换中文配置或者英文配置
setLocale("zh_CN");

// 默认导出一个对象，其中包含 yup 规则方法，及 vee-validate 中所有方法（注意：默认导出必须使用默认导入）
export default { yup, ...veeValidate };
```

3. **在需要使用校验的 vue 文件中，默认导入 index.ts 文件导出的内容**

```typescript
// 默认导入整个对象
import v from "@/plugins/validate/index";
// 解构出该对象中需要用到的方法
const { Form, Field, ErrorMessage, yup } = v;

// 当校验通过时触发
const onSubmit = (value: any) => {
  console.log("校验成功", value);
};
```

4. **在 vue 页面中，利用组件的形式，搭配原生的 vee-validate 规则包（@vee-validate/rules）中的校验规则，构建表单**

```html
  <!-- 必须使用 Form 组件包裹 -->
  <Form @submit="onSubmit">
    <!-- Field 和 ErrorMessage 的 name 必须保持一致，原生校验规则写在 Field 组件上 -->
    <Field name="account" type="text" placeholder="请输入手机号或邮箱" :rules="{ required, email }" />
    <!-- 校验的提示消息，若没有给对应规则自定义提示消息，则使用默认提示，且前缀名为 name 名（英文），需在配置中重命名 name -->
    <ErrorMessage name="account" />

    <Field name="password" type="password" placeholder="请输入密码" :rules="{ required, min: 6 }" />
    <ErrorMessage name="password" />

    <!-- 点击登陆时，会先校验，校验通过时，触发 Form 组件上的 submit 事件 -->
    <button>登录</button>>
  </Form>
```

5. **在 vue 页面中，利用组件的形式，搭配自定义校验方法（也可直接使用 yup 中的校验规则），构建表单**

**（Ⅰ）在之前的代码基础上，使用 yup 额外定义校验规则**

```typescript
// 默认导入整个对象
import v from "@/plugins/validate/index";
// 解构出该对象中需要用到的方法
const { Form, Field, ErrorMessage, yup } = v;

// 在之前代码基础上，新增内容（自定义校验规则）
const schema = {
  // es6 的对象写法，自定义校验方法
  account(value: string) {
    if (!value) return "请输入用户名";
    if (!/^[a-zA-Z]\w{5,19}$/.test(value)) return "字母开头且6-20个字符";
    return true;
  },
  // 使用 yup 中的校验规则
  password: yup.string().min(3).max(6).label("密码"),
};

// 当校验通过时触发
const onSubmit = (value: any) => {
  console.log("校验成功", value);
};
```

**（Ⅱ）在 Form 组件上新增 `:validation-schema="schema"` 属性，Field 组件上省略 rules 属性**

```html
  <!-- 必须使用 Form 组件包裹 -->
  <Form @submit="onSubmit" :validation-schema="schema">
    <!-- 若使用 yup 校验规则，则不需要在 Field 组件上定义 rules 属性 -->
    <Field name="account" type="text" placeholder="请输入手机号或邮箱" />
    <ErrorMessage name="account" />
     
    <Field name="password" type="password" placeholder="请输入密码" />
    <ErrorMessage name="password" />

    <!-- 点击登陆时，会先校验，校验通过时，触发 Form 组件上的 submit 事件 -->
    <button>登录</button>>
  </Form>
```



#### 十五、路由切换动画

**当路由切换时，需要实现动画效果，需要对当前需要使用切换动画的 router-view （路由出口）进行一些改造**

**注意：如果有 keep-alive 包裹着 component，则 Transition 组件包裹在 keep-alive 的外面**

**前提：先使用 cdn 引入 Animate.css 动画库（使用 BootCDN 搜索），将 link 链接放在项目的 index.html 文件中（或者使用模块化方式导入该包）**

```html
  <router-view #default="{ Component }">
    <Transition
      class="animate__animated"
      leave-active-class="animate__fadeOutLeft"
      enter-active-class="animate__fadeInRight"
      mode="out-in"
      appear
    >
      <component :is="Component"></component>
    </Transition>
  </router-view>
```

**属性解释：**

1. **leave-active-class 是 “ 路由离开动画 ”，enter-active-class 是 “ 路由进入动画 ”（固定属性，后面直接使用 animate 动画库中的类名即可）**
2. **由于路由切换时，离开和进入的动画是同时进行的，在一瞬间存在两个路由，会出现抖动现象，可使用 mode="out-in"，先执行离开动画，再执行进入动画**
3. **appear 可以让路由第一次加载时，就会使用动画**

**控制离开动画和进入动画的速度：**

```css
// 直接对 animate 动画库中的类名进行速度控制即可
.animate__fadeOutLeft {
  animation-duration: 0.3s;
}
.animate__fadeInRight {
  animation-duration: 0.3s;
}
```



#### 十六、组件和元素过渡动画

**vue 中的组件或者元素，在显示和隐藏时，可以设置离开和进入的动画（使用 Transition 标签包裹组件或元素即可）**

**便捷的使用方式：与路由切换动画一样，使用 leave-active-class 和 enter-active-class 这两个属性，配合 animate 动画库中的类名使用**

**自定义动画内容的方式：**

1. **给 Transition 添加 name 属性，取一个名称（如果没有名称，默认为 v）**
2. **通过固定的类名自定义离开和进入时的过渡效果（如不使用该方法，可给当前组件或元素设置类名，然后设置正常的 CSS 过渡属性即可）**

```css
.name名称-enter-active, .name名称-leave-active {
    transition: all 过渡动画本身的时间;
}
```

3. **通过固定的类名自定义离开和进入的动画**

```css
// 组件或元素未显示的动画类名
.name名称-enter-from, .name名称-leave-to {
    // 进入之前、离开之后的状态（未显示）
}

// 组件或元素已显示的动画类名
.name名称-enter-to, .name名称-leave-from {
    // 进入之后、离开之前的状态（已显示）
}
```

**尤其注意：由于离开和进入的动画是同时进行的，所以需要对离开的动画进行延迟执行，延迟的时间，就是过渡动画本身的时间**

```css
.name名称-enter-active {
    transition-delay: 过渡动画本身的时间;
}
```



#### 十七、自动按需加载 UI 组件库

**该插件用于自动加载 Element、Vant 等 UI 组件库中的组件，无需单独引入或全局引入，并且能够自动全局注册 src/components 文件夹下的组件**

1. **下载插件：yarn add unplugin-vue-components -D**
2. **在 vite.config.ts 文件中，配置插件，实现 UI 组件库的自动按需加载**

```typescript
// 必须导入该方法
import Components from 'unplugin-vue-components/vite';
// 从资源包中导入 Vant、Element 等组件库的资源包...
import { VantResolver, ElementPlusResolver } from 'unplugin-vue-components/resolvers';

export default {
  // 配置插件，数组第一项是配置 vue 内部插件，第二项是自动按需加载 UI 组件库的插件
  plugins: [
    vue(),
    Components({
      // 不额外生成类型声明文件
      dts: false,
      // 使用某个组件库的资源包，并忽略自动导入样式，在 main.ts 文件中，单独引入组件库样式文件（如果不忽略，让它自动导入样式，部分功能会异常）
      resolvers: [VantResolver({ importStyle: false })],
    }),
  ],
  其它配置...
};
```



#### 十八、数字滚动组件的封装

1. **下载依赖：yarn add gsap**
2. **在 src 下的 components 文件夹下，新建 ScrollCount.vue 文件，并注册为全局组件（全局注册的步骤不再详细阐述），并写入以下代码：**

```html
<script setup lang="ts" name="ScrollCount">
import { ref, watch } from "vue";
// 引入 gasp 相关方法
import { gsap } from "gsap";

// 将需要滚动的变量包装成一个对象，初始值为 0
const Obj = ref({ num: 0 });

const props = defineProps({
  // 真正的数据
  value: {
    type: Number,
    required: true,
  },
  // 保留小数点，默认为 0
  decimal: {
    type: Number,
    required: false,
    default: 0,
  },
});

// 监听父传子传递过来的真正数据的变化，立即执行
watch(
  () => props.value,
  () => {
    // 如果真正的数据存在，才执行
    if (props.value) {
      // 从初始值 0，滚动到真正的数据
      gsap.to(Obj.value, {
        duration: 0.5,
        num: props.value,
      });
    }
  },
  {
    immediate: true,
  }
);
</script>

<template>
  <!-- 保留的小数个数 -->
  <span>{{ Obj.num.toFixed(decimal) }}</span>
</template>

<style lang="scss" scoped></style>
```

3. **使用该组件：`<ScrollCount class="类名，可自定义样式" :value="真正的数据" :decimal="需要保留的小数点位数" />`**



#### 十九、元素进入可视区域时的动画效果

**使用自定义指令的进阶方法进行全局注册即可：**

```typescript
import { type App } from 'vue'

// 动画开始前的位置
const DISTANCE = 150
// 动画的持续时间
const DURATION = 1000

// 建立 WeakMap 对象，用于储存每个元素，以及该元素自己对应的动画（因为多个元素不能共用一个动画）
const animationMap = new WeakMap()

// 创建可视监听的实例，可使用 ob.observe(dom元素) 开启对该元素的可视监听
const ob = new IntersectionObserver((entries) => {
  // 所有被监听的元素，都会被追加到 entries 中
  for (const entry of entries) {
    // 遍历所有被监听的元素，如果当前元素处于可视范围内
    if (entry.isIntersecting) {
      // 从 WeakMap 对象中取出当前元素对应的动画
      const animation = animationMap.get(entry.target)
      // 并且播放动画
      animation.play()
      // 播放完动画后，关闭对当前元素的可视监听，后续再次从非可视区域到可视区域无动画效果
      ob.unobserve(entry.target)
    }
  }
})

// 判断元素是否在可视区域内的方法
function isBelowViewport(el: any) {
  const rect = el.getBoundingClientRect()
  console.log(
    '当前元素相对于视口的 top 值：',
    rect.top,
    '当前视口高度：',
    window.innerHeight
  )
  // 当前元素相对于视口的 top 值，大于（超出）当前视口高度，就说明该元素不在可视范围之内
  return rect.top > window.innerHeight
}

export default {
  install(app: App) {
    app.directive('move', {
      // 当前自定义指令绑定元素被挂载时的钩子函数
      mounted(el) {
        // 当前绑定元素如果在可视区域内，不执行任何操作
        if (!isBelowViewport(el)) {
          return
        }

        // animate API，第一个参数是数组，表示动画的两种状态，第二个参数是对象，表示动画的持续时间
        const animation = el.animate(
          [
            {
              transform: `translateY(${DISTANCE}px)`,
              opacity: 0
            },
            {
              transform: 'translateY(0)',
              opacity: 1
            }
          ],
          {
            duration: DURATION,
            easing: 'ease'
          }
        )
        // 动画默认为暂停状态
        animation.pause()
        // 将当前元素以及该元素对应的动画，添加到 WeakMap 对象
        animationMap.set(el, animation)
        // 开启对当前元素的可视监听
        ob.observe(el)
      },
      // 当前自定义指令绑定元素被销毁时的钩子函数
      unmounted(el) {
        // 关闭对当前元素的可视监听
        ob.unobserve(el)
      }
    })
  }
}
```



#### 二十、支付流程

1. **根据后端接口，渲染用户预支付的订单信息（还未生成订单，用户可选择优惠券，以及其它选项），后续会根据这些参数生成订单**
2. **点击选择支付方式的按钮，根据后端接口，无感生成订单 ID（这个过程中可以给该按钮使用 loading，增加用户体验），然后再打开选择支付方式的弹框，记录该订单 ID，最后选择支付方式，记录支付方式，并提前规定好支付完毕后的回跳地址（路由地址）**
3. **优化流程引导，如中途支付关闭，提示是否确认取消支付等等，如果取消支付，就清空订单 ID，并跳转到约定好的初始页面**
4. **点击支付后，将订单 ID、支付方式、回跳地址，作为支付接口的参数，该接口会返回一个加密的支付地址，通过 window.location.href = 加密地址，跳转到支付页面进行支付，支付完成后，会自动回跳到之前约定好的回跳地址（路由地址），并携带一些参数，其中包括是否支付成功的信息**
5. **根据是否支付成功的状态，做后续业务逻辑即可**



#### 二十一、第三方登录流程（QQ 登录）

1. **公司会申请 QQ 互联，向开发人员提供第三方登录的 appid，以及回跳地址（真实域名+路由地址）**
2. **在 index.html 文件中引入相关 SDK**

```html
<script
  src="https://connect.qq.com/qc_jssdk.js"
  data-appid="公司提供的 appid"
  data-redirecturi="公司提供的回跳地址"
></script>
```

3. **在页面中，书写如下代码，生成临时的 QQ 登录按钮**

```javascript
// 生成临时登录按钮
onMounted(() => {
  QC.Login({
    btnId: '自定义id名称'
  })
})

// 准备一个容器
<div id="自定义id名称">
```

4. **通过审查临时登录按钮元素，看到登录链接，复制下来，作为 a 标签的 href 跳转链接，a 标签里面可自定义元素及样式**
5. **由于回跳地址是 “ 真实域名+路由地址 ” 的形式，而本地项目为 “ 127.0.0.1（或 localhost）+路由地址 ” 的形式，因此需要在 host 文件下配置映射关系**

```html
// host 文件目录在 C:\Windows\System32\drivers\etc 下，新增如下映射关系，127.0.0.1 就等价于真实域名

127.0.0.1 真实域名
```

6. **在 vite.config.ts 中，设置端口号为 80，并设置允许访问 host**

```typescript
export default defineConfig({
  其它配置...
  server: {
    port: 80,
    host: true
  }
})
```

7. **点击第三方登录按钮，登录成功后，会回跳到约定好的路由地址（回跳的路由页面，因为是未登录状态，所以需要设置在白名单中，才可正常访问）**
8. **当登录成功后，回跳到该路由页面的的同时，会携带一些必要参数（openId），在该路由页面的 onMounted 生命周期中，通过 QQ 提供的 API，获取 openId，并记录下来**

```typescript
import { onMounted, ref } from 'vue'

// 需要记录的 openId
const openId = ref('')

// 是否需要绑定手机号的状态，如果为 true，则显示绑定页面
const isNeedBind = ref(false)

onMounted(() => {
  // 检查 QQ 登录是否成功
  if (QC.Login.check()) {
    // QQ 提供的 API 方法，用于获取 openId
    QC.Login.getMe((id) => {
      // 记录 openId
      openId.value = id
        
      // 调用后端提供的 QQ 登录请求，要求传入 openId
      自己封装的 QQ 登录请求方法(id)
        .then((res) => {
          // 登录成功，res 中会返回 token，把 token 保存在仓库中，并持久化，然后跳转到首页即可（和普通登录逻辑一样）
        })
        .catch(() => {
          // 登录失败，说明后端没有检索到该 QQ 用户，需要先进行绑定，显示绑定页面即可
          isNeedBind.value = true
        })
    })
  }
})
```

9. **在绑定页面进行绑定，绑定的请求，等价于登录的请求，也就是绑定成功后，接口会返回 token，后续的登录逻辑和平常一样**



#### 二十二、前端实现加密和解密

1. **下载依赖：npm install crypto-js**

2. **加密和解密主要依赖于两个方法，在 utils 文件夹下新建 crypto.ts 文件，书写如下代码，在需要加密的地方调用相关方法即可：**

```typescript
import CryptoJS from 'crypto-js'

/**
 * 对数据进行加密处理
 * @param data 需要加密的数据，可以为任意类型
 * @param key 加密密钥，与后端规定好的自定义密钥，必须为8位，或8位的倍数
 * @returns 加密后的字符串
 */
export const encryptData = (data: any, key: string): string => {
  // 使用 CryptoJS 库的 AES 加密方法对数据进行加密处理，得到加密后的结果
  const encryptRes = CryptoJS.AES.encrypt(
    JSON.stringify(data),
    CryptoJS.enc.Utf8.parse(key),
    {
      mode: CryptoJS.mode.ECB,
      padding: CryptoJS.pad.Pkcs7
    }
  )

  // 将加密后的结果转换为字符串，并返回
  return encryptRes.toString()
}

/**
 * 解密数据
 * @param ciphertext 密文
 * @param key 解密密钥
 * @returns 解密后的数据
 */
export const decryptData = <T>(ciphertext: string, key: string): T => {
  try {
    // 使用 CryptoJS 库的 AES 解密方法对数据进行解密处理
    const decryptedBytes = CryptoJS.AES.decrypt(
      ciphertext,
      CryptoJS.enc.Utf8.parse(key),
      {
        mode: CryptoJS.mode.ECB,
        padding: CryptoJS.pad.Pkcs7
      }
    )

    // 将解密后的结果转换为字符串（此处比较特殊，虽然是字符串方法，但转好的字符串是 JSON 字符串）
    const decryptedString = decryptedBytes.toString(CryptoJS.enc.Utf8)
    // 最后将 JSON 字符串转变为真正的数据
    const decryptedData = JSON.parse(decryptedString)
    // 返回最终解密后的数据，在调用解密方法时，可传递一个泛型，用于指定返回结果的类型
    return decryptedData as T
  } catch (error) {
    // 如果解密失败，抛出一个错误
    throw new Error('解密失败：' + error)
  }
}
```



#### 二十三、埋点技术

##### （一）统计上个路由（页面）的停留时长

1. **在 utils 文件夹下新建 routerTime.ts 文件，并书写以下代码：**

```typescript
// 记录初始时间
let startTime = Date.now()

// 统计上个路由（页面）停留时间的方法
export const countRouterTime = (to: any, from: any) => {
  // 跳转新路由的时候记录当前时间
  const currentTime = Date.now()
  // 如果上个路由（页面）是 xxx 路由（页面）
  if (from.path === '/control') {
    // 根据实际业务需求，执行某些操作，(currentTime - startTime) / 1000，拿到的是上个路由（页面）停留的时间
    console.log(
      `由${from.path}页面跳转到${to.path}页面，上个页面停留了${
        (currentTime - startTime) / 1000
      }秒`
    )
  }
  // 重置初始时间
  startTime = Date.now()
}
```

2. **在路由权限文件中，按需引入上述文件中的 countRouterTime 方法，在路由前置守卫中，最开始的地方调用该方法即可，传入 to 和 from 作为参数**
