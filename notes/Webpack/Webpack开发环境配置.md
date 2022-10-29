# Webpack开发环境配置

在之前章节里的例子,都是通过执行npm webpack 命令完成一次打包后,在本地一个HTML文件里引入打包后的资源,然后在浏览器打开这个HTML文件来观察打包后的效果.这种方式更接近于生产环境的打包.

在我们平时的开发工作中，如果每次修改代码后，都要执行npx webpack然后手动刷新浏览器，那势必会严重影响我们的开发效率。 

因此我们需要一些提高开发效率的机制，例如，修改代码后不需要手动执行构建命令而是会进行自动构建，重新构建后也不需要手动刷新浏览器而是会自动刷新等等。

其实，Webpack本身已经提供了上面所述的自动构建功能，它需要使用Webpack的监听模式，而监听模式默认是不开启的。在我们开启Webpack监听模式后，修改代码保存的那一刻，Webpack会监测到，然后自动执行构建。

除了使用Webpack自身的监听模式，我们在日常开发中更常用的是webpack-dev-server这个开发工具，Webpack社区给其命名叫做DevServer。webpack-dev-server是一个npm包，在我们安装后通过在Webpack配置文件里增加与entry和output等同级的devServer配置项，即可开启DevServer的功能。 

DevServer除了支持文件监听和浏览器自动刷新外，还可以做到模块热替换，这是一个非常高效的功能。

DevServer开启了一个本地HTTP服务器，一来可以请求处理和转发，二是就不需要使用本地文件预览了。

DevServer还支持Source Map，在本地开发的时候，非常方便调试工作。

webpack-dev-server其实是基于webpack-dev-middleware和Express的，如果想要更灵活的本地开发环境配置，也可以使用webpack-dev-middleware。

除了webpack-dev-server等工具外，本章的重点内容也包括开发环境的入口entry与出口output该如何配置，该使用哪些loader与plugin以及这些loader与plugin该如何配置等。

## 一. 文件监听与webpack-dev-server:

###   1.Webpack文件监听模式watch:

 Webpack提供了开启文件监听的模式,在我们的工程代码修改时,会自动重新构建出新的打包文件.

要开启文件监听模式最简单的方法就是在启动的时候加上 --watch 这个参数.

```js
webpack --watch
```

这个例子的Webpack配置文件我们已经很熟悉了，从a.js作为入口文件打包，a.js的内容很简单，定义一个变量name然后输出。代码如下 

```js
// webpack.config.js
  const path = require('path');
  module.exports = {
    entry: './a.js',
    output: {
      path: path.resolve(__dirname, ''),
      filename: 'bundle.js'
    },
    mode: 'none'
  };
```

```js
// a.js
  let name = 'Jack';
  console.log(name);
```

现在我们在该工程目录下命令行执行npx webpack --watch，这个时候就开启了Webpack的文件监听。仔细观察命令行窗口，会发现Webpack构建信息与以往的不同。

一个是会提示webpack is watching the files；另外一个就是该命令行构建程序不会自动退出，这个时候不能再执行其它命令。

Webpack开启的文件监听的方式，除了通过在命令webpack后面加--watch参数的形式，也可以在其配置文件里进行开启。配置文件如何配置在这里就不介绍了，因为我们极少会用到，因为在平时工作中我们用到的是更高级的webpack-dev-server。 

2. ### webpack-dev-server:

   webpack-dev-server是Webpack官方提供的一个工具，一般也称它为DevServer。安装并使用它后会在本地开启一个web服务器，可以用来处理网络请求。 

webpack-dev-server是一个npm包，我们只需要在命令行执行下面的命令就可以完成安装 

```js
npm i -D webpack-dev-server@3.11.0
```

完成安装后，就可以启动webpack-dev-server了。我们在项目根目录执行npx webpack-dev-server，就启动了webpack-dev-server。

在启动webpack-dev-server时，它会自动帮我们启动webpack并读取本地的webpack配置文件，同时它会启用webpack的文件监听模式。

它告诉我们工程正运行在本地localhost的8080端口下，webpack的输出目录'/'被服务器加载。 

webpack-dev-server服务器默认使用工程根目录的index.html作为首页，现在工程根目录没有index.html文件，所以服务器加载的网页信息是工程目录。 

我们把my.html重命名为index.html，在手动刷新浏览器，就可以看到网页正常显示index.html内容了。 



