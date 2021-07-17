# vant-validate
在使用 [vant](https://github.com/youzan/vant) 做服务号开发时，发现van-form的验证能用但不太好用：

1. 每个van-field要写 :rules 
2. 内置的验证规则少
3. 根据表单验证的结果来控制按钮状态麻烦
   
所以根据以上这些特点，写了个指令解决 使用 [async-validator](https://github.com/tmpfs/async-validate) 来处理 vant-form 的验证。

本人也是小白一枚，欢迎大家指正和修改代码。

## 准备工作
1. 安装 async-validate
```
npm i async-validate
```

2. 引入验证指令
在 main.js 中引入下载好的指令，比如：
```
import './directive/VantFormValidate';
```

## 如何使用
1. 模板部分

```html
<van-form :model="submitData" :rules="vRules" v-van-form-validate="formValid">
  <van-field
    v-model="submitData.username"
    name="username"
    label="用户名"
    placeholder="用户名"
  />
  <van-field
    v-model="password"
    type="password"
    name="password"
    label="密码"
    placeholder="密码"
  />
  <div style="margin: 16px;">
    <van-button round block type="info" native-type="button" :disabled="!isValid" @click="submit">提交</van-button>
  </div>
</van-form>

```
好的，大家应该看出了一些变化，van-form 加入了一些新的 Props 而这些都是该验证指令**必须**添加的：
### Props
参数                |说明|类型
--------------------|-------------|----------
model               | 需要验证的表单值（即需要提交部分的表单项的值）| object
rules               | 验证规则，写法请参照 [async-validator](https://github.com/tmpfs/async-validate/blob/master/README.md#rules) 官方文档 rules部分 | object
v-van-form-validate | 验证指令的结果返回，将会传出参数 `boolean`, `object` | function


2. 代码部分
```javascript
export default{
   name: 'demo',
   data(){
     return {
       vRules:{
           username:{required:true, max:60, message: '用户名为必填项，最大60个字符'},
           password:[
              {required:true, message: '密码必填'},
              {min:8, max:200, message: '密码长度8~200之间'}
           ]
       },
       submitData:{
           username: '',
           password: ''
       },
       isValid:false
     }
   },
   methods:{
       formValid(isValid, errors){
           this.isValid = isValid;
           console.log(errors);
       },
       submit(){
          console.log(this.submitData);
       }
   }
}

```
### 各变量如何使用
变量名可换只需要在van-form对应位置修改即可，这里例子只是说明一下各部分如何配合运行：
参数|说明
----|----
vRules | 这里用来写验证规则，更多写法请参考 [async-validator](https://github.com/tmpfs/async-validate/blob/master/README.md#rules) 官方文档 rules部分，指令会通过`:rules="vRules"`拿到          你写的验证规则
submitData | 验证的表单域所拿到的值 指令通过`:model="submitData"`拿到需要验证的所有字段
formValid| 主要将验证的结果作为传入参数，指令通过`v-van-form-validate="formValid"`返回验证结果，`formValid(boolean, object)`用于控制按钮或自己处理错误消息
isValid | 控制表单按钮状态的值，由定义的`formValid`函数控制

## 注意事项
`van-field`和`van-form`上的这些 Props 需要保持一致性比如这些位置，以username为例，v-van-form-validate会根据这些位置来进行验证以及处理错的显示:
```html
<van-field
     name="username"
     v-model="submitData.username"
     ……
/>
```
```javascript
data(){
   return {
      vRules:{
         username:{}，
         ……
      }
   }
}
```




