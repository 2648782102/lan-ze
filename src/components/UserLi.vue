<template>
  <!-- 茶楼，用户创作诗词展示 -->
  <section>
    <div class="dox-left">
      <h4>{{ name }}</h4>
      <p>{{ details }}</p>
    </div>
    <div v-if="userObj" class="avatar">
      <el-avatar class="elavatar" v-loading="loading" :src="imgsrc" icon="UserFilled" />
      <span class="pe-2 pt-3">{{ userObj.user_name }}</span>
    </div>
  </section>
  <el-divider />
</template>

<script>
import { onMounted, ref } from "vue";
import axios from "axios";
import hoad1 from "../assets/hoad/hoad1.jpg";
import hoad2 from "../assets/hoad/hoad2.jpg";
import hoad3 from "../assets/hoad/hoad3.jpg";
import hoad4 from "../assets/hoad/hoad4.jpg";
import hoad5 from "../assets/hoad/hoad5.jpg";
import hoad6 from "../assets/hoad/hoad6.jpg";
import hoad7 from "../assets/hoad/hoad7.jpg";
import hoad8 from "../assets/hoad/hoad8.jpg";
import hoad9 from "../assets/hoad/hoad9.jpg";
import { createClient } from '@supabase/supabase-js';

  const supabaseUrl = 'https://caqm0ri5g6h17oismvp0.baseapi.memfiredb.com'
  const supabaseKey = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJyb2xlIjoiYW5vbiIsImV4cCI6MzE5Mzk3MzkyOSwiaWF0IjoxNjU2MDUzOTI5LCJpc3MiOiJzdXBhYmFzZSJ9.pDkmh4NNw19c9lJIQUpidkadJHrKhlsB-e3ZdHGD6tA'
  const supabase = createClient(supabaseUrl, supabaseKey)

export default {
  name: "UserLi",
  props: ["title", "name", "details", "id"],
  setup(props) {
    let userObj = ref({});

    let imgsrc = ref(null);

    let loading = ref(false);  // 加载
    onMounted(() => {
      loading.value = true;
    //   获取资源列表

    async function asyncFunc() {

    let { data: response, error } = await supabase           // 请求数据
      .from('user_message')
      .select('head_img')
      .eq('id', props.id)

    // author:作者   // tag:标签  // poemName:诗名  // textBody:正文

    loading.value = false

    switch (response[0].head_img) {
            case "hoad1.jpg":
              imgsrc.value = hoad1;
              break;
            case "hoad2.jpg":
              imgsrc.value = hoad2;
              break;
            case "hoad3.jpg":
              imgsrc.value = hoad3;
              break;
            case "hoad4.jpg":
              imgsrc.value = hoad4;
              break;
            case "hoad5.jpg":
              imgsrc.value = hoad5;
              break;
            case "hoad6.jpg":
              imgsrc.value = hoad6;
              break;
            case "hoad7.jpg":
              imgsrc.value = hoad7;
              break;
            case "hoad8.jpg":
              imgsrc.value = hoad8;
              break;
            case "hoad9.jpg":
              imgsrc.value = hoad9;
              break;
            default:
              imgsrc.value = hoad1;
          }

    }

    asyncFunc();

      // axios
      //   .get(`https://lan-ze-user.vercel.app/api/user/getUpload?id=${props.id}`)
      //   .then((response) => {
      //     loading.value = false;
      //     userObj.value = response.data;
      //     switch (userObj.value.head_img) {
      //       case "hoad1.jpg":
      //         imgsrc.value = hoad1;
      //         break;
      //       case "hoad2.jpg":
      //         imgsrc.value = hoad2;
      //         break;
      //       case "hoad3.jpg":
      //         imgsrc.value = hoad3;
      //         break;
      //       case "hoad4.jpg":
      //         imgsrc.value = hoad4;
      //         break;
      //       case "hoad5.jpg":
      //         imgsrc.value = hoad5;
      //         break;
      //       case "hoad6.jpg":
      //         imgsrc.value = hoad6;
      //         break;
      //       case "hoad7.jpg":
      //         imgsrc.value = hoad7;
      //         break;
      //       case "hoad8.jpg":
      //         imgsrc.value = hoad8;
      //         break;
      //       case "hoad9.jpg":
      //         imgsrc.value = hoad9;
      //         break;
      //       default:
      //         imgsrc.value = hoad1;
      //     }
      //   })
      //   .catch((error) => {
      //     console.log(error);
      //   });
    });

    return {
      userObj,
      imgsrc,
      loading,
    };
  },
};
</script>

<style scoped>
span {
  float: right;
}
p,
span {
  color: rgb(152, 152, 152);
}
section {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.dox-left  {
    width: 80%;
    overflow: hidden;
}
.dox-left>p {
    width: 40%;
    height: 1.6rem;
    overflow: hidden;
}
.elavatar {
  width: 2.6rem;
  height: 2.6rem;
  font-size: 1.5rem;
}
</style>