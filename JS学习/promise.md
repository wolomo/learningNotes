# Promise

### 回调产生  -  异步函数

文件读取

json的发起





###回调函数解决异步问题

```
const  fs = require('fs')
const  path = require('path')


function  getfilebypath(fpath,Callback) {
    fs.readFile(fpath,'utf-8',(err,data)=>{
        Callback(err,data)
    })
}

getfilebypath(path.join(__dirname,'./1.txt'),(err,data)=>{
    if (err) return console.log(err)
    console.log(data)
    getfilebypath(path.join(__dirname,'./2.txt'),(err,data)=>{
        if (err) return console.log(err)
        console.log(data)
    })
})

```



### promise函数的定义

```
//new Promise的时候,就已经运行了里面的函数,
//resolve  运行成功的回调
//reject   运行失败的回调


function  getFile(fpath) {
    return new Promise(function (resolve,reject) {
        fs.readFile(fpath,'utf-8',(err,data)=>{
            if(err)  return reject(err)
            resolve(data)
        })
    })
}

getFile('./ 1.txt').then(function (data) {
    console.log(data)
},function (err) {
    console.log(err.message)
})
```







###promise函数的使用

```
const  fs = require('fs')
const  path = require('path')
function  getFile(fpath) {
    return new Promise(function (resolve,reject) {
        fs.readFile(fpath,'utf-8',(err,data)=>{
            if(err)  return reject(err)
            resolve(data)
        })
    })
}


//在函数运行完以后,return一个promise对象
//然后在调用该对象的then方法
getFile('./1.txt').then(function (data) {
    console.log(data)
    return getFile('./23.txt')
}).then(function (data) {
    console.log(data)
    return getFile('./3.txt')
}).then(function(data){
    console.log(data)
}).catch(function (err) {
    console.log(err.message)
})
```

