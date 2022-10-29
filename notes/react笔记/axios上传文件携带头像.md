1.Html代码:

```html
<input  name="file" type="file" accept="image/png,image/gif,image/jpeg" @change="update"/>

```

2.JS代码:

```js
import axios from 'axios'

     // 添加请求头
update (e) {   // 上传照片
      var self = this
      let file = e.target.files[0]
      /* eslint-disable no-undef */
      let param = new FormData()  // 创建form对象
      param.append('file', file, file.name)  // 通过append向form对象添加数据
      param.append('chunk', '0') // 添加form表单中其他数据
      console.log(param.get('file')) // FormData私有类对象，访问不到，可以通过get判断值是否传进去
      let config = {
        headers: {'Content-Type': 'multipart/form-data'}
      }
     // 添加请求头
    axios.post('http://172.19.26.60:8081/rest/user/headurl', param, config)
        .then(response => {
          if (response.data.code === 0) {
            self.ImgUrl = response.data.data
          }
          console.log(response.data)
        })
    }
/*
http://172.19.26.60:8081/rest/user/headurl这里是你的服务器接口地址
*/

```

