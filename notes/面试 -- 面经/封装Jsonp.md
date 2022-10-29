## 手写Jsonp函数

**代码思路:**

1.准备带有padding的请求url,得到一个字符串dataStr.

2.构造script.

3.js代码使用document.createElement创建一个**script标签**.

4.在函数作用域中外界如何访问到?使用window.

## 初步版本:

```js
  <script>
    let jsonp = (url,data={},callback='callback')=>{
      let dataStr = url.indexOf('?') == -1 ? '?' : '&';
      for(let key in data){
        dataStr += `${data}=${data[key]}&`;
      }
      dataStr += `callback=`+callback;

      let oScript = document.createElement('script');
      oScript.src = url + dataStr;
      document.body.appendChild(oScript);

      window[callback] = (data)=>{
        console.log(data);
      }
    }

    jsonp('https://photo.sina.cn/aj/index?a=1',{
      page:1,
      cate:'recommend'
    })
  </script>
```

虽然实现了效果,但还可以在window[callback]这部分进行优化.

## 优化版本:

```js
<script>
  let jsonp = (data,url,callback="callback")=>{
    let dataStr = url.indexOf('?') == -1 ? '?' : '&';

    for(let key in data){
      dataStr += `${data}=${data[key]}&`;
    }
    dataStr += 'callback='+callback;

    let oScript = document.createElement('script');
    oScript.src = url + dataStr;
    document.body.append(oScript);

    return new Promise((resolve,reject)=>{
      window[callback] = (data)=>{
        try{
          resolve(data);
        }
        catch{
          reject(e);
        }finally{
          oScript.parentNode.removeChild(oScript);
        }
      }
    })
  }
  jsonp('https://photo.sina.cn/aj/index?a=1',{
            page:1,
            cate:'recommend'
        }).then(response=>{
            console.log(response,'-------then');
        })     
</script>
```

