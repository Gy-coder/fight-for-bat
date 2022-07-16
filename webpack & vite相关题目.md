# webpack 相关内容

1. webpack的构建流程

    1. 初始化参数：从配置选项和shell中获得参数，合并参数得到初始化参数
    2. 开始编译： 根据初始化参数，生成complier对象，然后调用complier.run()开始编译
    3. 确定入口： 根据webpack.config.js 重的entry字段确定入口
    4. 编译文件： 根据入口文件，调用配置的loader对代码进行处理并分析依赖，然后对依赖用loader对代码进行转译，直到所有的文件都被处理
    5. 完成编译文件： 在经过第4步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系
    6. 输出内容： 根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，把内容变为chuck，再把每一个chuck变为一个文件，并加入到输出文件列表中
    7. 写入文件： 根据文件名和output输出路径，把文件写入到文件系统中

 2. webpack热更新的原理
 
    当文件被修改之后

    1. 文件系统通知webpack
    2. webpack重新编译模块，并且通知HMR(热更新模块) server
    3. HMR Server通过websocket通知HMR runtime需要更新，HMR runtime通过http请求更新jsonp
    4. HMR runtime替换更新的模块，如果没办法更新模块，则触发整个页面的刷新
  
 3. 什么是Tree shaking？ 如何做到Tree shaking

    **Tree shaking会在打包时，把未引用的代码剔除**

    Tree Shaking的原理

    1. ESmodules的代码是静态分析的，所以可以判断依赖
    2. 分析程序流，判断哪些代码是未引用的，把未引用的，未使用的代码删除

    如何做Tree Shaking‘

    1. 怎么删

      ```js
      import {deepClone} from 'loadsh ✅
      import _ from 'loadsh' ❌
      ```

    2. 怎么不删

      在 package.json 中配置 sideEffects，防止某些文件被删掉
        * 比如我 import 了 x.js，而 x.js 只是添加了 window.x 属性，那么 x.js 就要放到
        sideEffects 里
        * 比如所有被 import 的 CSS 都要放在 sideEffects 里

 4. 如何提高webpack的构建速度
 
    1. 在 DllPlugin 中把不用的代码提前打包
    2. 使用thread-loader或 HappyPack进行多线程打包
    3. 在开发环境中，在webpack.config.js 中把cache设置为true
    4. 在生产环境中，关闭不需要的流程，比如可以关闭 source map
    
  5. webpack和vite的区别
  
      在开发环境中

        1. vite充分利用了`<script type="modules"></script>`特性
        2. webpack使用babel-loader对代码进行打包

      在生产环境中

        1. vite基于rollup + esbuild基于内存打包(比如递归的打包vuejs)
        2. webpack 使用 babel 来打包 JS 代码，比 esbuild 慢很多很多很多

      文件处理时机

        1. vite 只会在你请求某个文件的时候处理该文件
        2. webpack 会提前打包好 main.js，等你请求的时候直接输出打包好的 JS 给你
        
        
6. 什么swc和esbuild

    1. swc
    
      实现语言： rust
      功能： 编译js/ts 打包js/ts
      优势： 比babel快20倍
      能否用于webpack： 能
      缺点 ： 不能打包css、svg等 & 不能对ts代码进行类型检查(tsc可以）
      
    2. esbuild
    
      实现语言： go
      功能： 编译js/ts 打包js/ts
      优势： 比babel快50倍
      能否用于webpack： 能
      缺点: 不能对 TS 代码进行类型检查 & 不能打包 CSS、SVG
      
      
7. loader 和 plugin的区别
  
     1. loader是由于浏览器只能识别js代码，而无法识别诸如css等代码，因此需要对代码进行转译，他的核心功能是输入代码内容，然后输出转译后的代码内容
     2. plugin是对webpack功能的拓展，通过调用tapable的钩子，在对应的时机执行自定义操作
     
8. 手写一个简单的markdown-loader

    ```js
    function markdown-loader(code){
      const html = marked(code)
      return `
        const str = ${html}
        export default str;
      `
    }
    ```
9. 手写一个简单的plugin

   ```js
   class FileListPlugin{
    apply(complier){
      complier.hooks.emit.tap("FileListPlugin",(compliation)=> {
        const { assets } = compilation
        let content = `## 文件名    大小 \r\n`
        for (const [filename, stat] of Object.entries(assets)) {
          content += `- ${filename}    ${stat.size()}\r\n`
        }
        assets[this.filename] = {
          source() { return content },
          size() { return content.length }
        })
     }
   }
   ```
      
      
10. es modules 和 commonjs的区别

    1. 用法不同

     es module 是 import / export， commonjs是require和module.exports

    2. 输出变量

      es modules 输出的是一个只读引用， 这个引用不可被修改，等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。

      commonjs 输出的是导出值的浅拷贝，如果输出的是引用类型，则修改导出值会改变原来导出的内容

    3. 运行时间

      commonjs会在运行时加载，require加载某个模块时，就会运行整个模块的代码，但是如果require两次，第二次会从缓存去取值

      es modules 模块在编译时，就能确定模块的依赖关系，以及输入和输出的变量。ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。
