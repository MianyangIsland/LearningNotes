# Webpack插件plugin

插件是用来扩展Webpack功能的. 插件是Webpack的骨干,Webpack自身也建立于插件系统之上.

在Webpack中使用插件非常简单,只需要在配置项里增加一个plugins项即可.plugins是一个数组,每一个数组元素是一个插件.

下面是一个简单的Webpack插件的使用，先引入clean-webpack-plugin插件，然后在plugins配置项里放入该插件的实例就可以使用了。

webpack.config.js

```js
const path = require('path');
  const { CleanWebpackPlugin } = require('clean-webpack-plugin');
  module.exports = {
    entry: './a.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'bundle1.js'
    },
    plugins:[
      new CleanWebpackPlugin()    
    ],
    mode: 'none'
  };
```

通常plugins数组每一个元素都是插件构造函数New出来的一个实例,根据每一个插件的特点,可能会需要向其参数里传递各种配置参数,这个时候需要参阅该插件的文档来进行配置.

现在广泛使用的插件都有默认的参数,可以免去配置,只有再需要特殊处理时,我们进行手动配置.

## 一. clean-webpack-plugin

​    1. clean-webpack-plugin是一个清除文件的插件.在每次打包后,磁盘空间会存有打包后的资源,在再次打包的时候,我们需要先把本地已有的打包后的资源清空,来减少它们对磁盘空间的占用.插件clean-webpack-plugin就可以帮我们做这个事情.

​    2.安装 clean-webpack-plugin

    ```js
npm install --save-dev webpack@4.46.0  webpack-cli@3.3.12    
npm install --save-dev clean-webpack-plugin@3.0.0  
    ```

3. 使用 clean-webpack-plugin

   我们的Webpack配置文件如下,两个JS文件,a.js是入口文件,它使用import('./b/js')引入了b.js模块.

   webpack.config.js

   ```js
    var path = require('path');
     var { CleanWebpackPlugin } = require('clean-webpack-plugin');
     module.exports = {
       entry: './a.js',
       output: {
         path: path.resolve(__dirname, 'dist'),
         filename: 'bundle.js',
         // filename: 'bundle2.js',
       },
       plugins:[
         new CleanWebpackPlugin()    
       ],
       mode: 'none'
     };
   ```

   在使用该插件的时候，我们首先通过require('clean-webpack-plugin')引入该插件，接着在plugins配置项里配置该插件。配置该插件的时候通过 new CleanWebpackPlugin()就完成配置了，我们不传入任何参数，该插件会默认使用output.path目录作为需要清空的目录，会把该目录下的所有文件目录以及文件都清除。 

我们执行npx webpack完成打包，dist目录里有bundle.js和1.bundle.js两个文件。
接着我们把Webpack的输出文件名output.filename改成bundle2.js，再次执行npx webpack，会output.path路径目录里的文件清空进行打包。

打包完成后观察dist目录，我们发现dist目录里的bundle.js和1.bundle.js这两个文件不见了，新增加了bundle2.js和1.bundle2.js。这就是clean-webpack-plugin的作用，它把dist之前的内容清空，然后打包重新生成了新文件。

### 三. copy-webpack-plugin:

1. #### copy-webpack-plugin介绍:

   插件copy-webpack-plugin是用来复制文件的。在我们使用Webpack的时候，有一些本地资源，例如图片和音视频，在打包过程中没有任何模块使用到它们，但我们却想要把它们放在打包后的资源输出目录。预处理器loader是不适合做这种事情的，这个时候需要使用插件，copy-webpack-plugin这个插件就可以帮助我们完成这个事情。 

#### 2.安装 copy-webpack-plugin:

```js
npm install --save-dev webpack@4.46.0  webpack-cli@3.3.12    
  npm install --save-dev copy-webpack-plugin@6.4.1 
```

需要特别注意的是,不同版本的Webpack使用的copy-webpack-plugin版本是不一样的,因为copy-webpack-plugin使用的Webpack的API不一样.另外不同版本的copy-webpack-plugin的配置也有一些差别,这里要确保使用了与上方相同版本的npm包.

3. ### 使用 copy-webpack-plugin:

   webpack.config.js

   ```js
   var path = require('path');
     var CopyPlugin = require("copy-webpack-plugin");
     module.exports = {
       entry: './a.js',
       output: {
         path: path.resolve(__dirname, 'dist'),
         filename: 'bundle.js'
       },
       plugins:[
        new CopyPlugin({
          patterns: [
            { from: path.resolve(__dirname, 'src/img/'), to: path.resolve(__dirname, 'dist/image/') },
          ],
        }),
       ],
       mode: 'none'
     };
   ```

   在Webpack配置文件里，我们先require("copy-webpack-plugin")引入了copy-webpack-plugin，后续我们在plugins配置项里配置了该插件。 

   我们在目录'…src/img/''下放了一张图片，执行npx webpack后观察。我们发现该图片被复制到'…dist/image/'下了。 

   插件copy-webpack-plugin在使用的时候需要传入参数，该参数是一个对象。使用该插件进行复制，最重要的是要告诉它，需要从那个文件夹复制内容，以及要复制到的目标文件目录。

   参数对象的patterns属性就是来设置从哪儿复制以及复制到哪儿的。该属性是一个数组，数组每一项是一个对象，对象的from属性就是从哪儿复制，to属性就是复制到什么地方。观察上面的Webpack配置，很容易理解。

   如果要从多个文件目录复制内容，就在patterns数组里设置多个对象即可。

   

