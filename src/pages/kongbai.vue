<template>
  <article>
    <!-- 空白页，用于跳转刷新页面 -->
    
  </article>
</template>

<script>
import { useRouter } from "vue-router";

export default {
    name: 'kongbai',
    setup() {
        const $router = useRouter()
        function abc() {
            $router.back()
        }

        abc()
    }
}
</script>

<style scoped>
* {
    box-sizing: border-box;
  }

  article {
    width: 100%;
    min-height: 700px;
    display: flex;
    justify-content: center;
  }
</style>