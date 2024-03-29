# 依赖预打包（实验性）

> 注意：依赖预打包**仅适用于 Node.js 项目**，面向 Browser 的项目请勿使用

依赖预打包是指将项目的依赖、以及依赖的依赖，提前编译成项目的产物之一，而项目的源码也改为直接从预打包产物中引入依赖。

这是近年来社区 Node.js 项目流行的依赖处理方式，它可以给我们带来以下优点：

1. **NPM 包发布后安装体积更小、速度更快**。由于 NPM 包的依赖树往往十分复杂，我们依赖的只是 A 这棵树，安装的却可能是一片森林，但却不是每棵树都会被用到。而依赖预打包能将依赖打包成单个文件，让一片森林压缩成一棵大树，再跟随我们的项目一起发布 NPM，达到体积更小、速度更快的目的。
2. **提升项目稳定性**。相信大家都碰到过『昨天还好好的，今天怎么就挂了』的情况，即便底层依赖的更新再小心翼翼，也无法验证上层项目的所有场景，更何况有些底层依赖发版还不一定遵循 semver 的约定。而依赖预打包可以将『底层依赖更新』这件事由被动变为主动，需要更新的时候我们再重新预打包一份便是，从而大大提升项目稳定性。
3. **NPM 包发布后安装 0 warning**。由于 NPM 安装会进行 `peerDependencies` 的校验，可能会产生大量的警告，依赖预打包后不需要安装，用户在使用我们的 NPM 包时也不会再看到警告信息了。而我们作为 NPM 包开发者，在开发阶段安装的时候仍然是可以审查这些警告信息的。

但依赖预打包要处理的情况往往比想象中复杂，我们会遇到 `dynamic require/import`，也会遇到相邻产物文件的读取，这都可能使得依赖预打包失败、或者产物不可用。

father 已经尽可能地在处理这些 case，但仍然改变不了它**现阶段只能作为实验性功能**的事实，所以请谨慎在项目中使用依赖预打包能力，也欢迎将遇到的问题反馈到 [issue](https://github.com/umijs/father-next/issues/28) 中、帮助 father 改进。

延伸阅读：[云谦公众号文章：coa 和依赖锁定](https://mp.weixin.qq.com/s/KbmpzvoB1yJlNDEO1p_fJQ)

## 如何构建

只需要使用 `prebundle` 配置项，再执行 `father prebundle` 即可产出依赖预打包产物：

```js
// .fatherrc.js
export default {
  prebundle: {
    deps: ['pkg-a', 'pkg-b'], // 需要预打包的依赖包名，需要安装在 `devDependencies` 中
    output: 'compiled', // 默认输出目录，有自定义需求时才需配置
  },
};
```

更多配置项可参考 [配置项](../config.md)。
