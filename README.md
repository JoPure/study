# study

项目过程中遇到的问题和学习点

# 枚举

由于多页面有时需要共同的枚举选择，html 需要显示枚举值，下拉框 label，表格，或者属性判断，有时根据枚举值判断，使用枚举方式进行统一管理会更方便开发与维护。
在 common 里创建 enmu.js 文件，根据使用情况来判断是否进行 main 里全局引用，或是 vue 文件里单独引用

```javascript
// 报送状态
export const SUBTASKSTATUS = Object.freeze({
  noReport: '1', // 未填报
  reported: '2', // 已填报
  reportSuccess: '3', // 上报成功
  reportFailure: '4', // 上报失败
  pending: '5', // 审批中
  rejected: '6' // 审批拒绝
})
```

根据枚举值判断可如下使用

```javascript
import * as E from '@/common/enum'
    componentName () {
      switch (this.type) {
        case E.SUBTASKSTATUS.noReport:
          return 'tpl'
        case E.SUBTASKSTATUS.reportSuccess:
          return 'todoMagTpl'
        case E.SUBTASKSTATUS.reportFailure:
          return 'todoWarnTpl'
        case E.SUBTASKSTATUS.pending:
          return 'todoEarlyTpl'
        default:
          return 'todoReportTpl'
      }
    }
```

```javascript
// 落户任务状态
settleTaskStatusEnum: new Enum()
  .add('ALL', '全部', null)
  .add('SETTLE', '待落户', 0)
  .add('SETTLED', '已落户', 1)
  .add('CANCEL', '已取消', 2)
```

假如落户任务状态在表单内使用

```html
<el-select
  style="width: 99%"
  v-model="query.taskStatus"
  clearable
  placeholder="请选择"
>
  <-- 传统做法 -->
  <el-option value="0" :label="待落户 "></el-option>
  <el-option value="1" :label="已落户 "></el-option>
  <el-option value="2" :label="已取消"></el-option>

  <-- 基于枚举类的方法 -->
  <el-option
    v-for="item in enums.settleTaskStatusEnum"
    :key="item.value"
    :value="item.value"
    :label="item.label"
  >
  </el-option>
</el-select>
```

可以避免为每个枚举值进行判断后，再取其 label，后端返回 taskStaus:1

```html
<el-table-column prop="taskStatus" label="任务状态">
  <template slot-scope="scope">
    <-- 传统做法：定义function通过switch或者if判断并返回label --> {{
    switch(scope.row.taskStatus) case 0:... case 1... case 2.... }} <--
    基于枚举类的方法 --> {{
    enums.settleTaskStatusEnum.getLabelByValue(scope.row.taskStatus) }}
  </template>
</el-table-column>
```

通过定义字典的情况：

```javascript
// 定义字典
const map = { boolean: ['否', '是'] }
// 或者
const map = { boolean: { 0: '否', 1: '是', 2: '不知道' } }
```

```html
// 调用
<div>{{map.boolean[item.enable]}}</div>
```

# promise

promise 是一个对象，代表异步操作的成功或者失败
promise 有三种状态，成功，失败，

- resolve (true)
- reject (false)
- pending(等待)
  
  promise 里的 then 会跟随 resolve 成功的回调
  catch 则是 reject 失败的回调

```javascript
function readyPormise() {
  return new Promise(function(resolve, reject) {
    var state = document.readyState
    if (readyState === 'interactive' || readyState === 'complete') {
      resolve()
    } else {
      window.addEventListener('DOMContentLoaded', resolve)
    }
  })
}
readyPormise().then(function() {
  console.log('success')
})
readyPormise().catch(function() {
  console.log('failure')
})
```

## promise.then() 理解

也可以利用 then(onFulfilled，onRejected)方法来执行的，第一个方法就是成功状态的标志，第二个方法是失败的状态标志

```javascript
// 方法调用
readyPormise(true).then(
  function(msg) {
    console.log(msg)
  },
  function(error) {
    console.log(error)
  }
)
```

当有多个的时候也可以 then 多个,当然每次调用一次 then 或者 catch 都会返回不一样的 promise 对象
多个 then 连接在一起的时候函数会严格按照顺序执行，resolve-then-then-then,其中如果有一个失败或者错误可能会暂停在当前 then 的回调里

```javascript
readyPormise(true)
  .then(
    function(msg) {
      console.log(msg)
    },
    function(error) {
      console.log(error)
    }
  )
  .then(function(msg) {
    console.log(msg)
  })
  .then(function(msg) {
    console.log('1')
  })
```

## promise.catch() 理解

Promise.catch()方法是 promise.then(undefined,onRejected)方法的一个别名，
该方法用来注册当 promise 对象状态变为 Rejected 的回调函数。

```javascript
var promise = Promise.reject(new Error('message'))
promise.catch(function(error) {
  console.log(error)
})
```

## promise.all() 理解

Promise.all 可以接受一个元素为 Promise 对象的数组作为参数，当这个数组里面所有的 promise 对象都变为 resolve 时，该方法才会返回。

```javascript
var promise1 = new Promise(function(resolve) {
  setTimeout(function() {
    resolve(1)
  }, 3000)
})
var promise2 = new Promise(function(resolve) {
  setTimeout(function() {
    resolve(2)
  }, 1000)
})
Promise.all([promise1, promise2]).then(function(value) {
  console.log(value) // 打印[1,2]
})
```

# await async

-<code>await</code>

await 返回 Promise 对象的处理结果。它只能在异步函数 async function 中使用。
await 表达式会暂停当前 async function 的执行，等待 Promise 处理完成。
若 Promise 正常处理(fulfilled)，其回调的 resolve 函数参数作为 await 表达式的值，继续执行 async function。
若 Promise 处理异常(rejected)，await 表达式会把 Promise 的异常原因抛出。
另外，如果 await 操作符后的表达式的值不是一个 Promise，则返回该值本身

-<code>async</code>

```javascript
 async initData () {
      let res = await this.getIndName().catch(e => false)
      if (!res) {
        return
      }
 }
 getIndName () {
   // 请求方法封装
    return apiCap.getCompanyCode(this.companyId).then((res) => {
      if (!res || res.length <= 0) {
        return Promise.reject(new Error(false))
      }
      return Promise.resolve(true)
    }).catch(e => Promise.reject(e))
 }
 getIndName(){
   // 请求函数不封装  注意此时return 的方法里要加个true
    return this.httpRequest({
      url: '/login',
      method: 'POST',
      data: {
        userName: this.form.userName,
        password: this.form.password
      }
    }, true).then(res => {
      if (!res || res.length <= 0) {
        return Promise.reject(new Error(false))
      }
      if (!res.data) {
        return Promise.reject(new Error(err))
      } else {
        return Promise.resolve(res)
      }
    }).catch(e => {
      that.$message.error(e.message || '网络错误')
    })
 }
```

# 一些个人觉得需要多学习的方法或属性

-<code>Object.freeze()</code>

Object.freeze() 方法可以冻结一个对象。一个被冻结的对象再也不能被修改；
冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。
此外，冻结一个对象后该对象的原型也不能被修改。freeze() 返回和传入的参数相同的对象。

```javascript
const obj = {
  prop: 42
}

Object.freeze(obj)

obj.prop = 33
// Throws an error in strict mode
console.log(obj.prop)
// obj.prop = 42
```

-<code>Object.assign()</code>

Object.assign() 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

```javascript
const target = { a: 1, b: 2 }
const source = { b: 4, c: 5 }

const returnedTarget = Object.assign(target, source)

console.log(target)
// expected output: Object { a: 1, b: 4, c: 5 }

console.log(returnedTarget)
// expected output: Object { a: 1, b: 4, c: 5 }
```
