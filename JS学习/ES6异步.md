# ES6异步

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

### Promise.all()

- 将多个Promise对象实例包装，生成并返回一个新的Promise实例
- promise数组中所有的promise实例都变为resolve的时候，该方法才会返回
- 并将所有结果传递results数组中
- promise数组中任何一个promise为reject的话，则整个Promise.all调用会立即终止，并返回一个reject的新的promise对象

```
p1 = $.ajax(...)
p2 = $.ajax(...)
p3 = $.ajax(...)

Promise.all([p1, p2, p3]).then( results=> {
    let [a,b,c] = results;
    console.log(a);
    console.log(b);
    console.log(c);
});
```

### generator生成器函数

- 普通函数，一路到底
- generator函数，中间可以停，到哪停呢，用 yield 配合，交出执行权
- yield 有 放弃、退让、退位的意思
- 需要调用next()方法启动执行，需要遇到 yield 停, 踹一脚走一步
- generator函数前面加一个 `*` 两边可以有空格，或靠近函数或`function`
- 背后实际生成多个小函数，实现走走停停

```
function show() {
    console.log('a')
    console.log('b')
}
show() // 普通函数

function *show2() {
    console.log('1')
    yield
    console.log('2')
}
let genObj = show2()
genObj.next() // 1
genObj.next() // 2
genObj.next() // 最后了，没有结果
```

### async await

```
async function show() {
	let p1 =  await $.ajax(...)
	console.log(p1)
	let p2 =  await $.ajax(...)
	console.log(p2)
	let p3 =  await $.ajax(...)
	console.log(p3)
}

show()


//await 只能放在async里面
//await 后面可以是promise对象,也可以是字符串,数字,布尔值
//async返回的是一个promise对象
//只要await语句后面promise状态变为reject,整个async函数会中断



show().then(success=>{
    console.log(success)
},err=>{
     console.log(err)
})

```

### 处理async中失败的问题 = try/catch

```
async function show() {
	try{
        let p1 =  await $.ajax(...)
        let p2 =  await $.ajax(...)
        let p3 =  await $.ajax(...)
	}catch(e){
        ...
       
	}
	console.log(p1)
	console.log(p2)
	console.log(p3)
}
```

