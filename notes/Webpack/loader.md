# Webpack预处理器loader

预处理器loader本质上是一个函数,它接受一个资源模块,然后将其处理成Webpack核心能使用的形式.

在Webpack中,一切皆模块.Webpack在进行打包的时候,,对所有引入的资源文件,都当作模块来处理.

现在的Webpack,自身可以处理JS和JSON文件模块,它无法理解其他类型的文件模块.

## 一.loader配置:

1. ### Loader的关键配置:

   先看一下之前使用过的Webpack配置,我们使用style-loader和css-loader这两个loader来处理css.

   webpack.config.js里配置这两个loader

   ```js
     const path = require('path');
     module.exports = {
       entry: './a.js',  // a.js里引入了CSS文件
       output: {
         path: path.resolve(__dirname, ''),
         filename: 'bundle.js'
       },
       module: {
         rules: [{
           test: /\.css$/,
           use: ['style-loader', 'css-loader']
         }]
       },
       mode: 'none'
     };
   ```

   配置loader是在Webpack配置项module里进行的，这个配置项名为何叫module？Module是模块的意思，因此用这个名字可以表示这个配置项是用来解析与处理模块的。

   Module配置项里最重要的一个配置子项就是rules了，它定义了loader的处理法则。

   Rules是一个数组,数组每一项是JS对象,该对象有两个关键字属性test和use.

   test是一个正则表达式或正则表达式数组，模块文件名与正则匹配的会被use里的loader处理。 

   Use可以是字符串,对象或数组,表示要使用的loader.

   如果使用单一loader，那么可以取字符串，例如 use: 'babel-loader'。 

   如果该loader额外配置参数，那么可以取对象，额外参数放在options里（有部分loader放query里），例如 use: {loader: 'babel-loader', options: {…}}。 

   如果使用多个loader进行链式处理，那么可以取数组，数组每一项可以是字符串或对象，字符串或对象的使用同上。 

2. ### rules其他参数

   除了test和use这两个关键配置参数,rules还要exclude,inclde,resource,issure和enforce等参数.

   1. exclude 与 include:

      如果我们有一些文件不想被正则表达式匹配到的loader处理,那么我们可以配置exclude项,exclude的中文意思是排除.

      exclude的值是字符串或正则表达式,字符串需要是绝对路径.

      eg:

      ```js
      rules: [{
          test: /\.js$/,
          use: ['babel-loader'],
          exclude: /node_modules/,
        }]
      ```

      上面的配置表中,除了node_modules文件夹,对所有的以js为后缀名的文件模块使用babel-loader处理.

      include表示的意思正好与exclude相反,它表示支队匹配到的文件处理.

      ```js
      rules: [{
          test: /\.js$/,
          use: ['babel-loader'],
          include: /src/,
        }]
      ```

      上面的配置表示，只对src目录下以js为后缀名的文件模块使用babel-loader处理。 

   2. Enforce

      对同一类后缀名类型的文件,我们可以使用多个loader处理,例如处理CSS时使用['style-loader','css-loader'],loader的处理顺序是数组项**从后向前**.

      如果我们想强制某个loader最先处理或最后处理,可以使用enforce项.

      Webpack推荐使用的enforce项有两个,'pre'和'post'.

      pre表示这个loader在所有的loader之前执行,post表示这个在所有的loader执行后再执行.

      eg:

      ```js
       rules: [{
          test: /\.js$/,
          use: ['eslint-loader'],
          enforce: 'pre',
          exclude: /node_modules/,
        }]
      ```

      这个配置表示在所有处理js文件模块loader之前执行eslint-loader，这样我们可以在js代码未被处理的时候就进行eslint代码规范校验。 

   3. ### resource与issuer:

      Resource的中文意思是资源,issuer中文意思是发行人.在Webpack中被加载的模块我们称之为resource,而实施加载的模块我们称之为issuer.

      例如,我们在一个JS模块里引入了CSS模块,那么JS模块就是issuer,CSS模块就是resource.

      ```js
        // app.js
        import './src/reset.css'
      ```

      我们在app.js里引入了reset.css , 这里app.js是issuer,reset.css是resource.

      在之前使用过的loader配置里,起始已经使用了resource,那些test和exclude等配置是使用默认resource.下面的配置是等效的

      ```js
        rules: [{
          test: /\.css$/,
          use: ['style-loader', 'css-loader'],
          exclude: /node_modules/
        }]
        
        
       // 等效于
      
      
       rules: [{
          use: ['style-loader', 'css-loader'],
          resource: {
            test: /\.css$/,
            exclude: /node_modules/
          }
        }]
      ```

      issuer的使用:

      如果想指定只有src目录下的js引用的css被相应的loader处理,那么可以配置issuer,下面的配置是等效的:

      ```js
      rules: [{
          test: /\.css$/,
          use: ['style-loader', 'css-loader'],
          exclude: /node_modules/,
          issuer: {
            test: /\.js$/,
            include: /src/
          }
        }]
        
        //等价于
        
         rules: [{
          use: ['style-loader', 'css-loader'],
          resource: {
            test: /\.css$/,
            exclude: /node_modules/
          },
          issuer: {
            test: /\.js$/,
            include: /src/
          }
        }]
      ```

      

## 二.babel-loader

  babel-loader这个loader主要作用是在Webpack打包的时候,用Babel将ES6转换成ES5版本的.

1. 使用babel-loader的时候需要先安装相应的npm包.

```js
  # 安装Babel核心包以及babel-loader
  npm install @babel/core@7.11.1 babel-loader@8.1.0
```

我们选择使用@babel/preset-env这个Babel预设进行转码,所以我们也安装它.

```js
  npm install @babel/preset-env@7.11.1
```

2. 不使用babel-loader

   ```js
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

   安装相应的npm包后执行npm webpack 打包,我们发现即使没有使用babel-loader,Webpack仍然可以完成打包.

   但实际上,在Webpack打包JS文件的时候，如果不使用babel-loader，可以完成打包，打包后的ES6的代码不会转换成ES5。 

3.  使用babel-loader

   我们在配置文件webpack.config.js里加入bable-loader.

   ```js
    const path = require('path');
     module.exports = {
       entry: './a.js',
       output: {
         path: path.resolve(__dirname, ''),
         filename: 'bundle.js'
       },
       module: {
         rules: [
           {
             test: /\.js$/,
             exclude: /node_modules/,
             use: {
               loader: 'babel-loader',
               options: {
                 presets: ['@babel/preset-env']
               }
             }
           }
         ]
       },
       mode: 'none'
     };
   ```

   注意,我们除了使用了bbabel-loader,还增加了配置项options,该配置项与Babel配置文件里的是基本一致的,这里我们使用了@babel/preset-env。 

   安装相应的npm包后,执行 npm webpack打包,观察打包后的代码,我们发现ES6的代码已经转换成了ES5的， babel-loader配置项options除了正常的Babel配置文件的配置项，还可以开启缓存。通过增加cacheDirectory:true项，开启缓存，在初次打包后再次打包，如果JS文件未发生变化，可以直接使用初次打包后的缓存文件，避免了二次转码，有效提高打包速度。 

   ```js
    use: {
               loader: 'babel-loader',
               options: {
                 cacheDirectory: true,
                 presets: ['@babel/preset-env']
               }
             }
   ```

   

   对于Babel配置复杂的情况，我们可以在工程根目录单独建立一个Babel配置文件，例如babel.config.js。presets和plugins等配置项就不写在babel-loader的options里了，而是放在babel.config.js，babel-loader会自动读取使用其默认配置文件的配置。 



## 三. file-loader:

file-loader是一个通用文件处理loader.file-loader在webpack中的作用是,处理文件导入地址并替换成其访问地址,并把文件输出到相应位置.其中导入地址包括了JavaScript和CSS等导入语句的地址,例如jS的import和CSS的url().

1. ### file-loader处理JavaScript引入的图片.

```js
 module:{
        rules:[
           {
            'test':/\.css$/,
            'use':['style-loader','css-loader']
           },
           {
            'test':/\.jpg$/,
            'use':[{
                loader:'file-loader',
                options:{
                    name:'/images/[name].[ext]'
                }
            }],
           }
        ]
    },
```

这个例子的目标是用JS操作原生DOM，把一张图片插入到指定的div里。

首先是一个html页面index.html，我们在该html里引入了打包后的bundle.js，html里有一个id="main"的div。

接着看一下打包前的JS文件a.js。我们用import语法引入了一个jpg图片文件，原生的JS并不支持这种import语法引入图片的，这里就需要借助file-loader的能力了。稍后可以看一下我们的Webpack配置。

然后我们使用console.log(img)在控制台输出import后的img，它是一个字符串，字符串内容是file-loader处理后的图片访问地址。

最后我们用原生的DOM操作，把图片插入了id="main"这个div元素里。

html和a.js的代码如下所示。

```js
 <!DOCTYPE html>
  <html lang="en">
  <head>
    <script src="bundle.js"></script>
  </head>
  <body >
    <div id="main"></div>
  </body>
  </html>
```

```js
 // a.js
  import img from './sky.jpg';
  console.log(img);
  var dom = `<img src='${img}' />`;
  window.onload = function () {
    document.getElementById('main').innerHTML = dom;
  }
```

现在看一下我们的Webpack配置文件。配置很简单，入口文件就是我们上面的a.js，打包后生成的bundle.js放在项目根目录。

然后还有一个file-loader用来处理jpg文件，我们的sky.jpg图片也存放在项目根目录。

现在npm install file-loader@6.0.0安装好相应的npm包后，执行npx webpack打包.

可以看到，我们已经成功把图片插入到了div里，并且控制台输出的图片路径是 be735c18be4066a1df0e48a1173b538e.jpg 

###  2.file-loader处理CSS引入的图片:

 ```js
<!DOCTYPE html>
  <html lang="en">
  <head>
    <script src="bundle.js"></script>
  </head>
  <body>
    <div class="hello">Hello, Loader</div>
  </body>
  </html>


 // a.js
  import './b.css'


body {
    background: url(sky.jpg) no-repeat;
  }
 ```

```js
 // webpack.config.js
  const path = require('path');
  module.exports = {
    entry: './a.js',
    output: {
      path: path.resolve(__dirname, ''),
      filename: 'bundle.js'
    },
    module: {
      rules: [{
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },{
        test: /\.jpg$/,
        use: 'file-loader'
      }]
    },
    mode: 'none'
  };
```

上面两个例子只是介绍了file-loader在JS和CSS引入图片的时候该如何使用。

file-loader不仅仅可以处理图片资源，它本质功能是复制资源文件并替换访问地址，音视频等资源也可以使用它。

## 四.url-loader:

url-loader是file-loader的增强版,它支持file-loader的所有功能,另外还有一个特殊的功能.

### 1.url-loader的base64编码:

url-loader的特殊功能是可以计算出文件的base64编码，在文件体积小于我们指定的值（单位 byte）的时候，可以返回一个base64编码的DataURL来代替访问地址。

使用base64编码的好处是可以减少一次网络请求，提升页面加载速度。

举个例子，正常img引入图片地址是

```js
  <img src="be735c18be4066a1df0e48a1173b538e.jpg">
```

使用base64编码后，引入图片地址是"data:image/jpg;base64,iVBORw0KGgoA…"这种格式的，这样就不用去请求存储在服务器上的图片了，而是使用图片资源的base64编码。 

```js
  <img src="data:image/jpg;base64,iVBORw0KGgoA...">  <!-- 省略号...表示省略了剩下的base64编码数据 -->
```

在CSS中引入图片也是同样的道理。

这也是url-loader起这个名字的原因，因为它可以使用base64编码的URL来加载图片。

安装url-loader后:

```js
 // webpack.config.js
  const path = require('path');
  module.exports = {
    entry: './a.js',
    output: {
      path: path.resolve(__dirname, ''),
      filename: 'bundle.js'
    },
    module: {
      rules: [{
        test: /\.(jpg|png)$/,
        use: {
          loader: 'url-loader',
          options: {
            limit: 1024 * 8,
          }
        }
      }]
    },
    mode: 'none'
  };
```

这个配置里，我们使用url-loader处理jpg和png格式的图片，另外设置了参数limit，对于图片体积小于8KB（1024 * 8）的，我们转成base64编码的URL直接写入打包后的JS文件里。

这个例子里，我们引入了两张图片，分别是24KB的sky.jpg和4KB的chrome.png，经过url-loader处理后插入html文档里。

这个例子里，我们引入了两张图片，分别是24KB的sky.jpg和4KB的chrome.png，经过url-loader处理后插入html文档里。 

```js
 // a.js
  import img1 from './sky.jpg';
  import img2 from './chrome.png';
  console.log(img1);
  console.log(img2);
  var dom1 = `<img src='${img1}' />`;
  var dom2 = `<img src='${img2}' />`;
  window.onload = function () {
    document.getElementById('img1').innerHTML = dom1;
    document.getElementById('img2').innerHTML = dom2;
```

```js
 <!DOCTYPE html>
  <html lang="en">
  <head>
    <script src="bundle.js"></script>
  </head>
  <body >
    <div id="img1"></div>
    <div id="img2"></div>
  </body>
  </html>
```

我们用浏览器打开html并开启开发者工具，页面如下 

可以看到，因为chrome.png小于8KB，被转成base64编码直接在打包在JS文件里，而24KB的sky.jpg仍然通过file-loader来处理。 

### 2.file-loader与url-loader处理后的资源名称:

因为url-loader处理体积大于limit值的时候，本质是使用file-loader来进行处理的，接下来包括第三小节的内容对file-loader与url-loader都适用。 

file-loader生成的文件默认的文件名是"[contenthash].[ext]"，contenthash是资源内容hash值，ext是文件扩展名。我们可以通过设置name项来修改生成文件的名字。 

file-loader除了[contenthash]和[ext]这两个常用的占位符，还有[hash]和[name]，[hash]也是根据内容计算出的hash值，[name]是文件的原始名称。 

### 3.file-loader与url-loader处理后的资源路径:

file-loader默认使用output.publicPath作为资源访问地址.

当然，也可以在file-loader的配置项options里配置publicPath参数，它会覆盖output.publicPath。 

```js
 // webpack.config.js
  const path = require('path');
  module.exports = {
    entry: './a.js',
    output: {
      path: path.resolve(__dirname, 'dist'),
      filename: 'bundle.js'
    },
    module: {
      rules: [{
        test: /\.(jpg|png)$/,
        use: {
          loader: 'url-loader',
          options: {
            limit: 1024 * 8,
            name: '[name]-[contenthash:8].[ext]',
            publicPath: './dist/'
          }
        }
      }]
    },
    mode: 'none'
  };
```

注意，这里我们的output.path是path.resolve(__dirname, 'dist')，打包后的图片也会打到工程根目录下的dist文件夹。如果这个时候不设置publicPath，图片的访问路径就是默认的根目录，运行项目时就会发生找不到图片资源的故障。因此，我们设置图片的publicPath是'./dist/'，这样就能正常在本地运行项目了。

另外，可以观察项目打包后的图片名称，现在变成了sky-be735c18.jpg，与我们在url-loader里设置的一致。

