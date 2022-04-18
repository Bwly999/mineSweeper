<script lang="ts" setup>
// 获取所有.item元素
// 设置选中态样式
let items: NodeListOf<Element> | undefined
function setActive(a: Event) {
  // 遍历所有.item元素，移出active样式
  items!.forEach((item) => {
    item.classList.remove('active')
  })
  a.currentTarget?.classList.add('active')
  // 为当前选中项添加active样式
//   a.currentTarget.add('active')
}
const picList = ref([
  'https://images.unsplash.com/photo-1649601427084-5f5efaefc7a0?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80',
  'https://images.unsplash.com/photo-1649662231372-0ca430eb5b01?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=1470&q=80',
  'https://images.unsplash.com/photo-1649615644662-5f33602a08c2?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80',
  'https://images.unsplash.com/photo-1649615644662-5f33602a08c2?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80',
  'https://images.unsplash.com/photo-1649615644662-5f33602a08c2?ixlib=rb-1.2.1&ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&auto=format&fit=crop&w=774&q=80',
])
onMounted(() => {
  items = document.querySelectorAll('.item')
})

</script>
<template>
  <div class="container">
    <div v-for="pic, i in picList" :key="i" class="item active" :style="'background-image: url(' + pic + ');'" @click.prevent="setActive($event)">
      <div class="shadow" />
      <div class="content">
        <div class="icon">
          <!-- <i class="fa fa-sun-o" /> -->
          <a i-carbon-sun />
        </div>
        <div class="text">
          <div class="tit">
            这是标题
          </div>
          <div class="sub">
            这是一段描述
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
*{
    margin: 0;
    padding: 0;
}
.container{
    /* 弹性布局 默认水平排列 */
    display: flex;
    width: 90vw;
    max-width: 900px;
    height: 400px;
    /* 溢出隐藏 */
    overflow: hidden;
    justify-content: center;
    background: radial-gradient(circle at top center,#718497,#29323c);
}
.item{
    /* 相对定位 */
    position: relative;
    width: 60px;
    margin: 10px;
    cursor: pointer;
    border-radius: 30px;
    /* 保持原有尺寸比例，裁切长边 */
    background-size: cover;
    /* 定位背景图像为正中间 */
    background-position: center;
    /* 过渡效果：时长 贝塞尔曲线 */
    transition: 0.5s cubic-bezier(0.05,0.61,0.41,0.95);
    overflow: hidden;
}
.item .shadow{
    position: absolute;
    bottom: 0;
    left: 0;
    right: 0;
    height: 100px;
    transition: 0.5s cubic-bezier(0.05,0.61,0.41,0.95);
}
.item .content{
    display: flex;
    position: absolute;
    left: 10px;
    right: 0;
    bottom: 10px;
    height: 40px;
    transition: 0.5s cubic-bezier(0.05,0.61,0.41,0.95);
}
.item .content .icon{
    min-width: 40px;
    width: 40px;
    height: 40px;
    border-radius: 50%;
    background-color: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 16px;
}

.item .content .text{
    display: flex;
    flex-direction: column;
    justify-content: center;
    margin-left: 10px;
    color: #fff;
    width: 100%;
}
.item .content .text div{
    /* 超出显示省略号（需要设置width） */
    width: calc(100% - 70px);
    text-overflow: ellipsis;
    overflow: hidden;
    white-space: nowrap;
    opacity: 0;
    transition: opacity 0.5s ease-out;
}
.item .content .text .tit{
    font-weight: bold;
    font-size: 18px;
}
.item .content .text .sub{
    /* 设置过渡效果延迟时间 */
    transition-delay: 0.1s;
}
/* 选中态样式 */
.item.active{
    flex: 1;
    margin: 0;
    border-radius: 40px;
}
.item.active .shadow{
    background: linear-gradient(to top,rgba(0,0,0,0.35) 65%,transparent);
}
.item.active .content{
    bottom: 20px;
    left: 20px;
}
.item.active .content .text div{
    opacity: 1;
}
</style>
