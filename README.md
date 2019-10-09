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

# await async

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
