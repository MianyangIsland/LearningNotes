```js
function ajax(methods,url,yibu=true){
    return new Promise((resolve,reject) => {
        var xhr = new XMLHttpRequest();
        xhr.open(methods,url,yibu);
        xhr.onreadystatechange = function(){
            if(xhr.readyState === 4){
                if((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304){
                    resolve(xhr.response);
                }
                else{
                    reject(xhr.status);
                }
            }
        }
    })
}
```

