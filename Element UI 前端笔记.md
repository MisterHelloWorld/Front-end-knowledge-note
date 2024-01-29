# Element UI 前端笔记



### 一、安装组件库并全局导入 Vue 项目

1. **安装依赖：npm i element-ui -S**
2. **在 main.js 文件中引入该组件库，并使用**

```javascript
import ElementUI from 'element-ui';
import 'element-ui/lib/theme-chalk/index.css';

Vue.use(ElementUI);
```



**前提须知：Element UI 官方文档中有 `Attributes（属性）`、`Events（事件）`、`Methods（方法）`三大核心模块**

**`属性` 是给组件标签使用（文档中会标注是哪个组件标签的属性）**

**尤其注意（重要）：属性的值除了 string 类型的数据，属性名前不需要加冒号，其它类型（number、boolean、function）都需要在属性名前加冒号**

**`方法` 是通过给组件标签绑定 `ref`，然后通过 `ref` 获取该组件，并调用该组件的`方法`**

**`事件` 是通过给组件标签绑定 `@事件名='回调函数'` 来使用**



### 二、Layout 布局

**布局组件通常应用于需要布局的地方，如使用 el-row 设置高度，或设置内部子元素的对齐方式等）**

**el-row 代表每一行，el-col 代表每一列，一行里面可以有多个列（el-row 包裹多个 el-col）**

1. **每一行，被规划为 24 等份，通过在行内的每个 el-col 上书写属性 `:span="份额（number类型）"` 进行设定（注意：所有列的 span 之和不超过 24）**

   **注意：span 之和等于 24 代表占满一行，没有空隙**

   **尤其注意：el-col  没有 span 属性，默认撑开剩余等份**

2. **给 el-row 加上属性 `:gutter="间隔（number类型）"`，要求 el-col 里必须有一个盒子（通常做法），相当于给 el-row 里的每一个 el-col 都设定了左右内边距，但实际上每个 el-col 还是紧贴在一起的，只是 el-col 里面的盒子因为外部 el-col 有左右内边距的原因，导致内部盒子与外部产生距离，因此两个相邻的 el-col 看起来似乎有了间隔，但本质上，并不是相邻的两个 el-col 产生了距离**

```html
// 给 el-row 设定 gutter 属性，el-row 里的每一个 el-col 都有 20 的左右内边距
<el-row :gutter="20">
  // 给 el-col 设定 span 属性，可以划分列的份额，但所有列的 span 之和不超过 24（等于 24 代表占满一行，没有空隙）
  <el-col :span="8"><div>该列占8份</div></el-col>
  <el-col :span="16"><div>该列占16份</div></el-col>
</el-row>
```

3. **当一行中所有列（el-col）的所占份额，即 sapn 之和小于 24 时，可以给其中 el-col（可以是多个）加上属性 `:offset="剩余份额（number类型）"` 使其向右偏移，相当于设定了一个左 margin，但所有 el-col 偏离的份额总数是 24 减去 span 之和的份数（即：sapn 之和 + 偏移份额 = 24）**

```html
    <el-row>
      // 所有 el-col 的 offset 和 span 所占的总份额等于 24
      <el-col :offset="1" :span="5"><div class="bg">占5份，向右偏移1份</div></el-col>
      <el-col :offset="9" :span="9"><div class="bg">占9份，向右偏移9份</div></el-col>
    </el-row>
```

3. **当一行中所有列的所占份额小于 24 时（仅考虑 span，一般不使用 offset 偏移），即一行没有占满，可以给 el-row 设置属性 `type="flex"` 开启 flex 布局，对其中的 el-col 进行 flex 控制，即给 el-row 设置 `justify="水平排列方式（string类型）"`，或给 el-row 设置 `align="垂直排列方式（string类型）"`**

   **justify（水平排列方式）的属性值：start / end / center / space-around / space-between**

   **align（垂直排列方式）的属性值：top / middle / bottom**

   **尤其注意：align 垂直排列方式，仅对于 el-row 有高度，且高度大于 el-col 里盒子的高度时生效（el-col 不用设宽高，会被内部的盒子宽高撑开）**
   
   **尤其注意：当有多个行（并不是指 el-row，而是包裹 el-row 的元素所占一行）的时候，且每行的长度都不一致时（el-row 受父元素宽度影响，虽然在其父元素内，仍然占满一行，但长度变短），针对每个 el-row 中的 el-col 使用 span 划分份额，参考的是 el-row 当前的宽度，即便使用 flex 布局，且通过给 el-row 设置水平排列方式为 end，从上到下每一行的 el-col 排列仍然会参差不齐（span 份额数值虽然一样，但 span 份额数值所参考的 el-row 宽度不一样，导致 el-col 的宽度不确定，多行比较，每一个 el-col 参差不齐），因此保险做法是，给 el-row 里的 el-col，设置固定宽度，而不是利用 span 划份额**

```html
    <el-row type="flex" justify="center">
      <el-col :span="5"><div class="bg">占5份</div></el-col>
      <el-col :span="9"><div class="bg">占9份</div></el-col>
    </el-row>
```

4. **每一列又可以嵌套新的行，并可以对新行中的列进行 flex 布局（flex 布局的前提是份额不占满）**

5. **给 el-col 设置属性 `:push="距离（number类型）"`，可以使其该列向右移动，但类似于相对定位的脱标，不会影响别的列的位置**
6. **给 el-col 设置属性 `:pull="距离（number类型）"`，可以使其该列向左移动，但类似于相对定位的脱标，不会影响别的列的位置**



### 三、Dropdown 下拉菜单

1. **下拉菜单的基础用法（触发菜单内容的方式默认为 hover，可通过在 el-dropdown 上设置属性 `trigger="click"` 更改为点击触发）**

```html
<!-- 下拉菜单组件 -->
<el-dropdown>
  <!-- 根级标题，可通过 el-dropdown-link 类名修改根级标题的样式 -->
  <span class="el-dropdown-link">
    XXXX根级标题
    <!-- 根级标题的后缀图标，可在icon图标中选择对应的类名，另外可通过 el-icon-arrow-down 类名微调图标样式，不可直接使用icon图标对应的类名修改 -->
    <i class="el-icon-arrow-down icon图标对应的类名"></i>
  </span>
  <!-- 菜单内容 -->
  <el-dropdown-menu slot="dropdown">
    <!-- 菜单选项 -->
    <el-dropdown-item>选项一</el-dropdown-item>
    <el-dropdown-item>选项二</el-dropdown-item>
    <el-dropdown-item>选项三</el-dropdown-item>
  </el-dropdown-menu>
</el-dropdown>

<style>
  // 修改根级标题的样式
  .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
    
  // 修改根级标题后缀图标的样式
  .el-icon-arrow-down {
    font-size: 12px;
  }
</style>
```

2. **如果需要将下拉菜单组件的根级标题样式替换成 button 按钮样式，或者其它样式，可直接替换（此处为默认插槽，可自定义修改菜单根级标题样式）**
3. **菜单选项默认点击后，菜单内容会隐藏，如需点击菜单选项后，菜单内容不隐藏，可在 el-dropdown 上设置属性 `:hide-on-click="false"` 即可**
4. **当点击不同菜单选项，触发不同菜单选项的逻辑，需要在每个 el-dropdown-item 上添加属性 `command="当前菜单选项对应的标志"`，并在 el-dropdown 上添加事件 `@command="自定义事件函数"`，该自定义事件函数会接收到一个形参 command，该形参就是当前被点击的菜单选项传递过来的 command（也就是当前被点击的菜单选项对应的标志），可通过判断 command 的字段是否等于某个标志，来执行不同逻辑**



### 四、MessageBox 弹框（消息确认框）

**场景：该弹框仅用于简单的确认，是否执行某段逻辑，选择确认或取消，触发执行或不执行，与弹出层不同（弹出层是点击打开弹出层，里面有其它内容和逻辑）**

```javascript
    事件处理函数 () {
      this.$confirm('提示内容', '标题', {
        // 确认按钮的文字
        confirmButtonText: '确定',
        // 取消按钮的文字
        cancelButtonText: '取消',
        // 提示内容的消息类型（可选值：success / info / warning / error）
        type: 'warning'
      })
        .then(() => {
          // 点击确认按钮后执行如下代码
          ...业务逻辑
          // 确认后的的消息提示
          this.$message({
            type: 'success',
            message: '确认后的消息提示'
          })
        })
        .catch(() => {
          // 点击取消按钮后执行如下代码（一般情况无业务逻辑，仅消息提示即可）
          ...业务逻辑
          // 取消后的的消息提示
          this.$message({
            type: 'info',
            message: '取消后的消息提示'
          })
        })
    }
```

**注意：this.$confirm 方法返回的是 Promise 对象，因此也可使用 await 阻塞后续代码，当点击确认按钮后，才会继续执行，但需要配合 try catch 捕捉异常**



### 五、Dalog 对话框（弹出层）

**用法简单，参考文档即可，但需要注意如下内容：**

**该组件使用频率极高，且通过 `:visible.sync = "布尔值"` 来控制该弹层的显示与隐藏，由于 .sync 的存在，当点击弹层的关闭按钮，可以直接关闭该弹层，但是 .sync 仅仅只能关闭弹层，如果需要在关闭弹层的同时，进行其它操作（如重置表单数据），则需要使用 @close 事件，该事件在弹层关闭时触发**



### 六、表单相关

**el-form 里面的每一行都要用 el-form-item，且该 el-form-item 上可以当作 div 来使用（利用行内样式设置子元素的对齐方式等），这样就无需额外使用布局组件（el-row 以及 el-col），对子元素进行布局，并且也可以把 el-form-item 当作 el-row，可以直接在里面写 el-col，且 el-form-item 万物皆可包裹（非 element 组件也能被它包裹），比如包裹富文本编辑器，但需要注意的是，被包裹的元素，需要使用 v-model 双向绑定**

**给 el-form 添加 label-width 属性，可以设置每一行标题的宽度**

**给 el-form-item 中的子元素如果是 el-input，可直接使用 style 设置 el-input 的宽度及其它样式**

**使用表单组件时，需要注意的问题：**

1. **定义表单数据，需要请求接口需要提交的字段是什么，表单数据要和接口规定的一致，而不能乱取名字**
2. **表单校验的先决条件：给 el-form 设置 model 属性并绑定表单数据，给 el-form 设置 rules 属性并绑定 “ 表单校验对象 ”，给 el-form-item 设置 prop 属性并绑定 “ 表单校验对象 ” 里的当前字段，给 el-form-item 里具体包裹的内容（如 el-input）使用 v-model 双向绑定表单数据里的当前字段**
3. **” 表单校验对象 “ 里的字段，要和表单数据里的字段保持一致，且必须一致，否则会报错**
4. **在 “ 表单校验对象 ” 里定义校验规则时，required 只能校验你有没有填，对于类似复选框这种 false 和 true 的情况，无法校验（内置规则中，并没有提供此种校验行为），因此需要在 rules 里对应规则（花括号）中，使用 validator 属性进行自定义校验，该属性对应的方法中，value 是当前校验字段的值**

```javascript
(rule, value, callback) => { 对 value 进行一些判断，满足条件执行 callback()，否则执行 callback(new Error('自定义错误消息'))}
```

5. **类似于 “ 我同意 xxx 协议 ” 这种功能，如果组件库没提供自定义校验的方法（如 Vant 组件库），那么只能先整体校验之后，再单独判断这个复选框是 true 还是 false，然后提交表单（一般是发送登录请求）**

6. **提交表单前（发送请求前）必须对表单进行整体校验，在 el-form 上绑定 ref 属性，调用该 ref 表单实例上的 validate 方法对表单整体校验（参考文档），尤其注意，该方法返回的是 Promise 对象，因此也可使用 await 阻塞后续代码，当校验通过后，才会继续执行**
7. **当使用 resetFields 重置表单的方法时，该方法只能重置 el-form-item 绑定了 prop 属性的表单项，当某一个表单项不需要校验，因此没设置 prop，从而导致重置表单不能完全重置，可以在重置表单后，再手动将表单数据里的每个字段设为空，或者给那一个表单项设置一个 prop 属性，只是不添加校验规则即可**

8. **如需对表单部分字段进行校验（如发送验证码，只校验手机号），或者非 element 表单组件不能通过内置校验规则自动触发校验（如富文本编辑器），则可以在 el-form 上绑定 ref 属性，调用该 ref 表单实例上的 validateField 方法，在不同的触发方式下（如点击事件或者失焦事件），手动触发校验**



### 七、表格相关

```html
<el-table :data="表格数据" style="width: 可设置表格整体宽度">
   // 有多少列就写多少个 el-table-column
   <el-table-column prop="当前行数据（对象）中的某一个属性" label="当前列标题" width="当前列宽度">
      // 需要自定义内容，则需要使用插槽，row是当前行数据
      <template #default="{row}">
        <span>当前行数据（对象）中的某一个属性，也就是 row 里的某一个属性</span>
        其它自定义内容
      </template>
   </el-table-column>
</el-table>
```

**表格的行内编辑：如果需要实现表格的行内编辑，则需要对表格数据进行改造，即给请求返回的数据中每一个对象增加一个布尔值的变量，用来控制当前那一项是否可编辑（通过插槽中的 row 拿到当前布尔值变量，利用 v-if 和 v-else 判断是否显示文字内容还是 input 框），然后编辑状态下的 input 框不能双向绑定自己的原数据，因此在改造请求返回的数据时，需要增加一个变量保存和自己一样的数据，专门给自己那一行的 input 框使用 v-model 进行双向绑定，这样每一行数据，在编辑状态下，都有自己单独的 v-model 绑定的数据，不会影响原数据**

**表格嵌套树形：表格也可支持树形的形式，需要把表格数据转换成树形数据，然后给 el-table 添加一个 row-key 属性，并绑定表格数据中每个对象里可辨别的唯一标识符（每个对象里面可能会有 id 存在且唯一，可绑定该 id，即 row-key = ' id '），el-table 上 添加 default-expand-all 属性可以使树形表格默认展开**