<template>
  <div id="login">
    <div class="login-wrap">
      <ul class="menu-tab">
        <li v-for="item in menuTab" :key="item.id" :class="{'current': item.current}" @click="toggleMeneu(item)">{{item.txt}}</li>
      </ul>
      <!--表单-->
      <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" class="login-form" size="medium">

        <el-form-item prop="username" class="item-form">
          <label>邮箱</label>
          <el-input type="text" v-model="ruleForm.username" autocomplete="off"></el-input>
        </el-form-item>

        <el-form-item prop="password" class="item-form">
          <label>密码</label>
          <el-input type="text" v-model="ruleForm.password" autocomplete="off" minlength="6" maxlength="20"></el-input>
        </el-form-item>

        <el-form-item prop="passwords" class="item-form" v-if="model==='register'">
          <label>重复密码</label>
          <el-input type="text" v-model="ruleForm.passwords" autocomplete="off" minlength="6" maxlength="20"></el-input>
        </el-form-item>

        <el-form-item prop="code" class="item-form">
          <label>验证码</label>
          <el-row :gutter="10">
            <el-col :span="15">
              <el-input v-model.number="ruleForm.code"></el-input>
            </el-col>
            <el-col :span="9">
              <el-button type="success" class="block">获取验证码</el-button>
            </el-col>
          </el-row>

        </el-form-item>

        <el-form-item>
          <el-button type="danger" class="login-btn block" @click="submitForm('ruleForm')">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
  import {stripScript,validateEmail,validatePass,validateCo} from '@/utils/validate';
  //import {stripScript} from '../../utils/validate';
  export default {
    name: "index",
    data(){

      //验证用户名为邮箱
      const validateUsername = (rule, value, callback) => {
        //let reg = /^([a-zA-Z]|[0-9])(\w|\-)+@[a-zA-Z0-9]+\.([a-zA-Z]{2,4})$/;
        if (value === '') {
          callback(new Error('请输入用户名'));
        } else if (validateEmail(value)) {
          callback(new Error('用户名格式有误'));
        } else {
          callback();
        }
      };
      //验证密码
      const validatePassword = (rule, value, callback) => {
        //console.log(stripScript(value))
        //过滤后的数据
        this.ruleForm.password = stripScript(value)
        value = this.ruleForm.password

        //let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/
          if (value === '') {
            callback(new Error('请输入密码'));
          } else if (validatePass(value)) {
            callback(new Error('密码为6-20位数字+字母!'));
          } else {
            callback(); //true
          }
      };

      //再次验证密码
      const validatePasswords = (rule, value, callback) => {
        //v-show :如果模块值为login,直接通过
        if(this.model === 'login'){
          callback();
        }

        //console.log(stripScript(value))
        //过滤后的数据
        this.ruleForm.passwords = stripScript(value)
        value = this.ruleForm.passwords

        //let reg = /^(?!\D+$)(?![^a-zA-Z]+$)\S{6,20}$/
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value !== this.ruleForm.password ) {
          callback(new Error('两次密码不一致!'));
        } else {
          callback(); //true
        }
      };
      //验证验证码
      const validateCode = (rule, value, callback) => {
        this.ruleForm.code = stripScript(value)
        value = this.ruleForm.code

        if (value ==='') {
          return callback(new Error('请输入验证码'));
        }else if(validateCo(value)){
          return callback(new Error('验证码格式有误'));
        }else{
          callback();
        }
      };
      return {
        menuTab:[
          {txt:'登录',current:true,type:'login'},
          {txt:'注册',current:false,type:'register'}
        ],
        //模块值
        model:'login',
        //表单的数据
        ruleForm: {
          username: '',
          password: '',
          passwords:'',
          code: ''
        },
        rules: {
          username: [
            { validator: validateUsername, trigger: 'blur' }  //失去焦点时会触发validatePass方法
          ],
          password: [
            { validator: validatePassword, trigger: 'blur' }
          ],
          passwords: [
            { validator: validatePasswords, trigger: 'blur' }
          ],
          code: [
            { validator: validateCode, trigger: 'blur' }
          ]
        }
      }
    },
    created() {

    },
    mounted() {
    },
    methods:{
      //vue 数据驱动视频渲染
      //js  操作DOM元素
      toggleMeneu(data){
        this.menuTab.forEach(elem =>{
          elem.current = false  //先给menuTab里的每个设置成false
        });
        //高光
        data.current = true;
        //修改模块值
        this.model=data.type;
      },
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
    },
  }
</script>

<style lang="scss" scoped>
  #login {
    height: 100vh;
    background-color: #344a5f;
}
  .login-wrap{
    width: 330px;
    margin: auto;
  }
  .menu-tab{
    text-align: center;
    li{
      display: inline-block;
      width: 88px;
      line-height: 36px;
      font-size: 14px;
      color: #fff;
      border-radius: 2px;
      cursor: pointer;
    }
  .current{
    background-color: rgba(0,0,0,.1);
  }
  }

  .login-form{
    margin-top: 29px;
    label{
      display: block;
      font-size: 14px;
      color: #ffff;
      margin-bottom: 3px;
    }
    .item-form{
      margin-bottom: 13px;
    }
    .block{
      width: 100%;
      display: block;
    }
    .login-btn{
      margin-top: 19px;
    }
  }


</style>
