<template>
  <!-- 注销账号 -->
  <section v-loading='loading' element-loading-text="注销中..." class="card">
    <el-form :model="form" :rules="rules" class="p-5" label-position="left">
      <div class="text-center mb-4">
        <h1 style="color: rgb(53, 228, 170);">兰泽诗词<i class="fas fa-feather-alt"></i></h1>
      </div>
      <el-form-item prop="user" label="账号">
        <el-input v-model="form.user" />
      </el-form-item>
      <el-form-item prop="password" label="密码">
        <el-input v-model="form.password" show-password />
      </el-form-item>
      <div>
        <Vcode :show="VcodeShow" @success="VcodeSuccess" @close="VcodeClose" @fail="VcodeFail" />
        <button class="yan-zheng" @click="btnVcode" type="button">验证</button>
      </div>
      <div class="addData pt-3">
        <el-link @click="routeClick('login')">
          <i class="far fa-edit me-1"></i>登录
        </el-link>
        <div>
          <el-link @click="routeClick('adduser')" class="ms-2">
            <i class="fas fa-pencil-alt me-1"></i>注册
          </el-link>
          <el-link @click="routeClick('datauser')" class=ms-2>
            <i class="far fa-trash-alt me-1"></i>修改密码
          </el-link>
        </div>
      </div>
      <div class="mt-4 submit">
        <button class="col-4 col-md-4" type="button" @click="deleteClick">确认注销</button>
        <button class="col-4 col-md-4" type="button" @click="back">取消</button>
      </div>
    </el-form>
  </section>
</template>

<script>
  import { reactive, ref } from 'vue'
  import { useRouter } from "vue-router";
  import Vcode from "vue3-puzzle-vcode";
  import { ElMessage, ElMessageBox } from 'element-plus'
  import axios from "axios";
  import supabase from '../../function/supabase.js'

  export default {
    name: 'deleteuser',
    components: {
      Vcode
    },
    setup() {
      // 路由
      const $router = useRouter()

      // 表单信息双向绑定
      const form = reactive({
        user: '',
        password: '',
      })

      // 注册/修改/注销，跳转
      function routeClick(value) {
        $router.push({
          name: value
        })
      }

      //取消操作，返回个人中心设置页
      function back() {
        $router.push({
          name: 'shezhi'
        })
      }

      // 验证规则
      const rules = reactive({
        user: [
          { required: true, message: '账号必须输入', trigger: 'change' },
          { min: 5, max: 20, message: '长度为5-20', trigger: 'change' },
        ],
        password: [
          { required: true, message: '密码必须输入', trigger: 'change' },
          { min: 5, max: 20, message: '长度为5-20', trigger: 'change' },
        ]
      })

      // 验证码是否显示
      let VcodeShow = ref(false)
      // 验证是否通过
      let VcodeOK = ref(false)
      function btnVcode() {
        VcodeShow.value = true
      }

      // 验证成功的回调
      function VcodeSuccess() {
        VcodeShow.value = false
        VcodeOK.value = true
      }

      // 验证失败的回调
      function VcodeFail() {
        VcodeOK.value = false
      }

      // 点击遮罩层外的位置触发
      function VcodeClose() {
        VcodeShow.value = false
      }

      const handleSubmit = async (user, password) => {

        let { data: response, error } = await supabase           // 请求数据
          .from('us_er')
          .select('*')
          .or(`user.eq.${form.user}`)

        try {
          loading.value = false

          if (response.length != 0) {
            if (response[0].password == form.password) {
              const response2 = await supabase
              .from('us_er')
              .delete()
              .or(`user.eq.${form.user}`)

              try {
                if (response2.status!=400&&response2.status!=409) {
                  const response3 = await supabase
                  .from('user_message')
                  .delete()
                  .or(`id.eq.${response[0].id}`)

                  try {

                    $router.push({
                    name: 'login'
                  })
                  loading.value = false;
                  ElMessage({
                    type: 'success',
                    message: '注销成功!'
                  })

                  }catch(error) {
                    console.log(error);
                  }

                }

              } catch (error) {
                console.log(error);
              }
            } else {
              loading.value = false;
              ElMessage({
                type: 'warning',
                message: '密码错误!'
              })
            }


          } else {
            loading.value = false;
            ElMessage({
              type: 'warning',
              message: '账号未注册!'
            })
          }

        } catch (error) {
          console.log(`请求失败了，原因${error}`);
        }
      }

      // 定义弹出确认框表单双向绑定值
      let loading = ref(false)    //加载
      let queRen = ref('')
      function deleteClick() {
        if (form.user == '' || form.password == '') {
          ElMessage({
            type: 'warning',
            message: '账号或密码为空!'
          })
        } else {
          // 判断是否已经验证过
          if (VcodeOK.value) {
            // 弹出输入确认框
            ElMessageBox.prompt('账户注销后不可找回，确认注销请在下方输入： 确认注销', '警告', {
              confirmButtonText: '确认',
              cancelButtonText: '取消',
              inputPattern: /确认注销/,
              inputErrorMessage: '请输入确认注销！',
              type: 'warning',
              icon: 'Delete'
            })
              .then(({ value }) => {   //单击确认
                if (value == '确认注销') {
                  loading.value = true
                  handleSubmit()
                  // axios.get(`https://lan-ze-user.vercel.app/api/user/deleteUser?user=${form.user}&password=${form.password}`)
                  //   .then(response => {
                  //     loading.value = false
                  //     if (response.data == 'OK') {
                  //       $router.push({
                  //         name: 'login'
                  //       })
                  //       ElMessage({
                  //         type: 'success',
                  //         message: '账户注销成功!'
                  //       })
                  //     } else {
                  //       ElMessage({
                  //         type: 'warning',
                  //         message: `${response.data}`
                  //       })
                  //     }
                  //   })
                  //   .catch(error => {
                  //     console.log(error);
                  //   })
                }
              })
              .catch(() => {   //单击取消
                ElMessage({
                  type: 'info',
                  message: '操作取消！',
                })
              })
          } else {
            ElMessage({
              message: '请先验证',
              type: 'warning',
            })
            // 弹出验证码模态框
            VcodeShow.value = true
          }
        }
      }

      return {
        form,
        routeClick,
        rules,
        VcodeSuccess, btnVcode, VcodeClose, VcodeFail,
        VcodeShow,
        deleteClick,
        queRen,
        back,
        loading
      }
    }
  }
</script>

<style scoped>
  .submit {
    height: 40px;
    display: flex;
    justify-content: space-around;
  }

  .submit>button {
    border: 0;
    background-color: white;
    transition: 0.3s;
  }

  .submit>button:hover {
    border-bottom: 5px solid rgb(53, 228, 170);
    ;
    border-radius: 10px;
    box-shadow: 2px -2px 2px rgba(142, 144, 150, 0.1);
    transition: 0.3s;
  }

  .addData {
    display: flex;
    justify-content: space-between;
  }

  .yan-zheng {
    width: 30%;
    border: 0;
    background-color: rgb(255, 255, 255);
    border-radius: 0.2rem;
    font-size: 0.8rem;
    transition: 0.4s;
    color: rgb(71, 31, 116);
    box-shadow: 0.2rem 0.2rem 0.2rem #d9d9d9,
      -0.2rem -0.2rem 0.2rem #f5f5f5;
  }

  .yan-zheng:hover {
    border-radius: 0.3rem;
    background: #ffffff;
    box-shadow: none;
    transition: 0.2s;
  }
</style>