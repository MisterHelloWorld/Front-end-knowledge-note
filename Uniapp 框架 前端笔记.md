# Uniapp 框架 前端笔记



#### 一、uniapp 响应式单位、UI 设计图软件的测量方式

**概念：uniapp 具有 rpx 和 upx 两个单位，这两个单位的基准值都是参考 750 px，可以响应式变化，进行移动端适配**

1. **当设计稿为标准设计稿（375 或 750）时，此时分两种情况，如果为 375 px，在 UI 设计图软件的自定义设置中，指定宽度为 `像素 * 2` 即可，然后正常测量元素大小，将 px 单位替换成 rpx 或 upx 单位，无需考虑换算问题，如果为 750 px，无需任何设置，直接测量并替换单位即可**
2. **当设计稿为非标准设计稿（宽度不是 375 的倍数，而是乱七八糟的数值）时，在 UI 设计图软件的自定义设置中，指定宽度为 750 px，正常测量元素大小，将 px 单位替换成 rpx 或 upx 单位，无需考虑换算问题**

**总结：任何尺寸的设计稿，将其转换为 750 px 尺寸即可（原因：一个 rpx 或 upx 等价于 750 px）**



#### 二、项目创建及标准规范

1. **使用 HBuilder 直接创建 vue3 项目即可（选择默认模板，勾选 vue3）**
2. **页面配置：需在 pages.json 文件下对页面进行配置（类似于路由，配置了才可访问页面），分为主包和分包，主包默认在 pages 文件夹下，右击 pages 文件夹，选择新建目录并新建页面（取消勾选 ” 创建同名目录 “，注意：需勾选 ” 在 pages.json 中注册 “，则无需在 pages.json 中进行主包页面配置）；分包可任意创建文件夹（名称任意），同样右击该文件夹，选择新建目录并新建页面（取消勾选 ” 创建同名目录 “，注意：取消勾选 ” 在 pages.json 中注册 “），然后在 pages.json 文件中与 pages 同级处进行分包配置即可**

```json
// pages.json 文件
{
  // 主包配置（自动生成）
  "pages": [ //pages数组中第一项表示应用启动页，参考：https://uniapp.dcloud.io/collocation/pages
		{
			"path": "pages/login/index",
			"style": {
				"navigationBarTitleText": "页面标题",
			}
		}
  ],

  // 分包配置
  "subPackages": [
    {
      "root": "分包文件夹名称1",
      "pages": [
        // 页面
        {
          "path": "页面路径（省略分包文件夹名称1，且无斜杠）",
          "style": {
            "navigationBarTitleText": "页面标题",
          }
        }
      ]
    },
    {
      "root": "分包文件夹名称2",
      "pages": [
        // 页面
        {
          "path": "页面路径（省略分包文件夹名称2，且无斜杠）",
          "style": {
            "navigationBarTitleText": "页面标题",
          }
        }
      ]
    }
  ]
  // 其它配置
}
```

3. **components 文件夹下的全局组件，需遵循 easycom 规范，即 `小写单词-小写单词` 的形式，且文件夹和文件，名称保持一致（例：super-table/super-table.vue），即可在其它页面无需引入和注册，直接使用该组件**
4. **在 uni.scss 文件中，可新增自己的全局变量，在任意位置可直接使用该全局变量**
5. **在 style 文件夹中，新建全局样式文件，可在 App.vue 文件的 style 标签中进行全局样式引入（注意：@import 形式），也可直接在 main.js 文件中引入**
6. **补充（极其重要）：当使用 git 管理项目时，在第一次提交前，便需要进行 git 忽略，否则会受 git 缓存影响，即便后续进行了忽略，依然导致忽略无效，在项目根目录处新建 .gitignore 文件，书写如下代码（说明：unpackage 是运行编译后的文件夹），然后再进行第一次 git 提交**

```yaml
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

node_modules
unpackage

.DS_Store
*.local
```

7. **Prettier 格式化（无需 Eslint）：在 HBuilder 编辑器的工具栏找到 ” 插件安装 “ 选项，安装 Prettier**

**（一）点击 Prettier 运行配置，在 ” 自定义 Prettier 的生效文件范围 “ 这一栏中，填入以下配置：**

```javascript
**/*.vue,**/*.nvue,**/*.ux,**/*.ts,**/*.tsx,**/*.less,**/*.sass,**/*.scss,**/*.js
```

**（二）在项目根目录处新建 .prettierrc 文件，书写如下代码：**

```yaml
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": false,
  "singleQuote": true,
  "vueIndentScriptAndStyle": true,
}
```

**（三）按 ctrl + k 进行格式化代码（选择使用 Prettier 进行格式化），可在 HBuilder 编辑器的工具栏找到 ” 自定义快捷键 “ 选项，将如下配置复制到右侧数组中，然后使用 alt + e 进行格式化**

```javascript
{"key":"alt+e","command":"editor.action.format","when":"!activeEditor.readonly && activeEditor.canFormat","override":true}
```

