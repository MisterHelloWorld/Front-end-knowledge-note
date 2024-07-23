# Uniapp 框架 前端笔记



#### 一、uniapp 响应式单位、UI 设计图软件的测量方式

**概念：uniapp 具有 rpx 和 upx 两个单位，这两个单位的基准值都是参考 750 px，可以响应式变化，进行移动端适配**

1. **当设计稿为标准设计稿（375 或 750）时，此时分两种情况，如果为 375 px，在 UI 设计图软件的自定义设置中，指定宽度为 `像素 * 2` 即可，然后正常测量元素大小，将 px 单位替换成 rpx 或 upx 单位，无需考虑换算问题，如果为 750 px，无需任何设置，直接测量并替换单位即可**
2. **当设计稿为非标准设计稿（宽度不是 375 的倍数，而是乱七八糟的数值）时，在 UI 设计图软件的自定义设置中，指定宽度为 750 px，正常测量元素大小，将 px 单位替换成 rpx 或 upx 单位，无需考虑换算问题**

**总结：任何尺寸的设计稿，将其转换为 750 px 尺寸即可（原因：一个 rpx 或 upx 等价于 750 px）**



#### 二、Uniapp 项目基础及标准规范

**使用 HBuilder 直接创建 vue3 项目即可（选择默认模板，勾选 vue3）**

**项目开发前需要做的事情：**

1. **登录微信公众平台：将自己的微信账号（非手机号）发给小程序管理员，把自己拉入项目成员，即可通过微信扫码方式登录微信公众平台**
2. **项目绑定 AppID：在 manifest.json 文件中，进行微信小程序配置，填入微信小程序 AppID（通过 “ 微信公众平台 - 开发管理 - 开发设置 - AppID ” 获取）**
3. **在 “ 微信公众平台 - 开发管理 - 开发设置 - 服务器域名 ” 中，配置 request 合法域名（请求环境地址），uploadFile 合法域名（请求环境地址），此处若不配置，不影响本地开发者工具中正常发送请求，但在真机中则无法正常发送请求**



##### （一）页面配置及创建

**概念：需在 pages.json 文件下对页面进行配置（类似于路由，配置了才可访问页面），分为主包和分包**

1. **主包配置：主包默认在 pages 文件夹下，右击 pages 文件夹，选择新建目录并右击该目录选择新建页面，页面名称为 index，取消勾选 ” 创建同名目录 “，此处需勾选 ” 在 pages.json 中注册 “，则无需在 pages.json 中进行主包页面配置**
2. **分包配置：分包可任意创建文件夹（名称任意），同样右击该文件夹，选择新建目录并右击该目录选择新建页面，页面名称为 index，取消勾选 ” 创建同名目录 “，此处需要取消勾选 ” 在 pages.json 中注册 “，然后在 pages.json 文件中与 pages 同级处进行分包配置即可**

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



##### （二）全局组件和局部组件

1. **全局组件：在 src 下的 components 文件夹（若无该文件夹，新建一个即可）中的全局组件，需遵循 easycom 规范，即 `小写单词-小写单词` 的形式，且文件夹和文件，名称保持一致（例：super-table/super-table.vue），即可在其它页面无需引入和注册，直接使用该组件**
2. **局部组件：在主包或分包目录下的页面文件夹中，新建 components 文件夹，组件名称遵循 `大写单词大写单词` 的形式（例：CardList.vue）**



##### （三）全局样式文件及全局样式变量

1. **全局样式文件：在 style 文件夹中，新建 global.scss 全局样式文件，可在 App.vue 文件的 style 标签中进行全局样式引入（注意：@import 形式），也可直接在 main.js 文件中引入**
2. **全局样式变量：在 uni.scss 文件中，可新增自己的全局变量，在任意位置可直接使用该全局变量**



##### （四）git 忽略管理（尤其重要）

**当使用 git 管理 uniapp 项目时，在第一次提交前，便需要进行 git 忽略，否则会受 git 缓存影响，即便后续进行了忽略，依然导致忽略无效，在项目根目录处新建 .gitignore 文件，书写如下代码（说明：unpackage 是运行编译后的文件夹），然后再进行第一次 git 提交**

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



##### （五）代码格式化

**Prettier 格式化（无需 Eslint）：在 HBuilder 编辑器的工具栏找到 ” 插件安装 “ 选项，安装 Prettier**

1. **点击 Prettier 运行配置，在 ” 自定义 Prettier 的生效文件范围 “ 这一栏中，填入以下配置：**

```javascript
**/*.vue,**/*.nvue,**/*.ux,**/*.ts,**/*.tsx,**/*.less,**/*.sass,**/*.scss,**/*.js
```

2. **在项目根目录处新建 .prettierrc 文件，书写如下代码：**

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

3. **默认快捷键为 ctrl + k 进行格式化代码（选择使用 Prettier 进行格式化）**

   **自定义格式化快捷键：可在 HBuilder 编辑器的工具栏找到 ” 自定义快捷键 “ 选项，将如下配置复制到右侧数组中，然后使用 alt + e 进行格式化**

```javascript
{"key":"alt+e","command":"editor.action.format","when":"!activeEditor.readonly && activeEditor.canFormat","override":true}
```



#### 三、Uniapp 中请求的二次封装

1. **下载依赖（与 axios 基本使用一致，有略微区别）：yarn add luch-request**
2. **封装 request.js 文件，写入以下代码（此处以 vue3 为例）：**

```javascript
// 引入使用仓库的通用方法
import useStore from '@/stores'

// 导入 luch-request 包
import luchRequest from 'luch-request'

// 实例化请求
const instance = new luchRequest({
	// 请求基地址
	baseURL: 'https://zscz.hahdc.cn/api/ledger',
    // 请求超时限制
	timeout: 10000,
	// 全局自定义参数（可在实际发请求中覆盖自定义参数默认的值，控制是否具有 loading 状态）
	custom: {
        // 是否具有 loading 状态
		withLoading: true,
	},
})

// 请求拦截器，携带 token
instance.interceptors.request.use(
	(config) => {
		// 请求前开启 loading 加载状态
		if (config.custom.withLoading) {
			uni.showLoading({
				title: '正在加载...',
				mask: true,
			})
		}
		// 拿到 user 仓库，稍后注入 token
		const { user } = useStore()
		// 如果 token 存在，才注入 token（避免 token 不存在，执行后报错）
		if (user.token) {
			// 尤其注意：axios 是 headers，这里是 header
			config.header['Authorization'] = user.token
		}
		return config
	},
	(err) => Promise.reject(err)
)

// 响应拦截器，剥离无效数据，进行无感刷新，或有感刷新
instance.interceptors.response.use(
    // 响应拦截器里的 res 值，小程序实机打印和开发者工具打印不一样结果不一样，以实机为准进行开发
	(res) => {
		// 请求结束后隐藏 loading 加载状态
		uni.hideLoading()

		// res 是请求返回的数据，luch-request 默认加了一层 data，因此对 res.data 进行解构，得到相应的请求状态、消息提示、以及真正想要的数据
		const { code, message } = res.data
		// 根据请求状态的成功与否决定下面的操作
		if (code === 200) {
			return res.data
		} else {
			// 请求状态为失败时，提示错误消息，并使用reject终止执行，该请求的后续操作同时也会终止(阻塞，无需捕获异常)
			uni.showToast({
				title: message,
				icon: 'none',
			})
			// 没有错误对象，可以new一个错误对象
			return Promise.reject(new Error(message))
		}
	},
	(err) => {
		// 如果响应失败，也隐藏 loading 加载状态
		uni.hideLoading()

		// 当响应拦截器发生处理错误，提示错误信息，并使用 reject 终止执行，该请求的后续操作同时也会终止
		uni.showToast({
			title: '请求加载失败！',
			icon: 'none',
		})
        ...中间其它操作（token 的有感刷新，无感刷新）
        // 此处使用的是错误方法里的 err
		return Promise.reject(err)
	}
)

export default instance
```



#### 四、开发常见功能及其用法

##### （一）获取当前页面路由（主包或分包配置的页面路径，可以理解为路由地址）

```javascript
// 获取路由历史数组
let routes = getCurrentPages()
// 基于路由历史数组获取当前页面路由
let currentPage = routes[routes.length - 1].route
```



##### （二）微信扫码进入小程序指定页面

1. **首先在 “ 微信公众平台 - 开发管理 - 开发设置 - 扫普通链接二维码打开小程序 ” 中，点击添加（此处添加的是二维码的相关配置）**

   **二维码配置如下：**

   **（1）协议类型：https 或 http（与后端提供的请求环境地址保持一致，一般为 https）**

   **（2）选择大小写：小写**

   **（3）二维码规则：填写一个特定的路径，前缀为请求环境地址，后缀名称自定义（https://请求环境地址/自定义后缀名称）**

   **（4）前缀占用规则：不占用**

   **（5）小程序功能页面：需要跳转的小程序页面路径**

   **（6）测试范围：体验版**

   **（7）测试链接（最多添加五个，开发过程中，只有内容为这些测试链接的二维码才能正常进行扫码）：https://请求环境地址/自定义后缀名称?参数=值**

2. **在二维码配置页中，找到校验文件进行下载，并交给后端，放到域名（请求环境地址）根目录下**

3. **把测试链接直接生成二维码（五个测试链接对应五个二维码），进行开发测试（可利用该网站进行生成：https://cli.im/url）**

4. **在对应需要扫码跳转的页面中，写入以下代码，获取二维码携带的相关信息，并利用该信息发送请求，做某些事情**

```javascript
	// 解析链接的查询字符串参数，并包装成对象
	const getQueryString = (url) => {
		const params = {}
		const queryString = url.split('?')[1]

		if (queryString) {
			const paramPairs = queryString.split('&')

			for (const pair of paramPairs) {
				const [key, value] = pair.split('=')
				params[key] = decodeURIComponent(value)
			}
		}
		return params
	}
    
    onLoad((query) => {
		// 获取二维码的原始链接内容
		const qrcodeLink = query.q ? decodeURIComponent(query.q) : ''
		console.log('通过扫码获取到二维码的原始链接内容：', qrcodeLink)
		// 利用封装好的 getQueryString 方法，拿到该二维码原始链接内容的参数（对象形式）
		const params = getQueryString(qrcodeLink)
		// 查看二维码链接携带的参数（只有手机能看到效果）
		console.log('当前二维码链接所携带的参数（对象形式）：', params)
        // 利用这些参数发送请求，做某些事情
        ......
	})
```

