# Uniapp 框架 前端笔记



#### （一）uniapp 响应式单位、蓝湖的测量方式

**概念：uniapp 具有 rpx 和 upx 两个单位，这两个单位的基准值都是参考 750 px，可以响应式变化，进行移动端适配**

1. **当设计稿为标准设计稿（375 或 750）时，此时分两种情况，如果为 375 px，在蓝湖自定义设置中，指定宽度为 `像素 * 2` 即可，然后正常测量元素大小，将 px 单位替换成 rpx 或 upx 单位，无需考虑换算问题，如果为 750 px，无需任何设置，直接测量并替换单位即可**
2. **当设计稿为非标准设计稿（宽度不是 375 的倍数，而是乱七八糟的数值）时，在蓝湖自定义设置中，指定宽度为 750 px，正常测量元素大小，将 px 单位替换成 rpx 或 upx 单位，无需考虑换算问题**