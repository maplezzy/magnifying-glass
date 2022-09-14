<script setup>
import { ref, reactive, onMounted } from 'vue';
const props = defineProps({
  imageArray: {
    type: Array,
    default: () => [],
  },
});
const boothRef = ref(null);
const mask = ref(null);
const bigImg = ref(null);
const bigImgBox = ref(null);
const state = reactive({
  boxShow: false,
});
const onMouseEnter = () => {
  state.boxShow = true;
};
const onMouseLeave = () => {
  state.boxShow = false;
};
const onMouseMove = (e) => {
  const x = e.pageX - boothRef.value.offsetLeft;
  const y = e.pageY - boothRef.value.offsetTop;
  let maskX = x - mask.value.offsetWidth / 2;
  let maskY = y - mask.value.offsetHeight / 2;
  // mask的x最大移动距离
  const maskXMaxMove = boothRef.value.offsetWidth - mask.value.offsetWidth;
  const maskYMaxMove = boothRef.value.offsetHeight - mask.value.offsetHeight;
  const bigImgXMaxMove = bigImgBox.value.offsetWidth - bigImg.value.offsetWidth;
  const bigImgYMaxMove = bigImgBox.value.offsetHeight - bigImg.value.offsetHeight;
  if (maskX <= 0) {
    maskX = 0;
  } else if (maskX >= maskXMaxMove) {
    maskX = maskXMaxMove;
  }
  if (maskY <= 0) {
    maskY = 0;
  } else if (maskY >= maskYMaxMove) {
    maskY = maskYMaxMove;
  }
  mask.value.style.left = maskX + 'px';
  mask.value.style.top = maskY + 'px';
  // 大图片移动距离 = mask的移动距离*大盒子最大移动距离 / mask的x最大移动距离
  const bixImgXMove = (maskX * bigImgXMaxMove) / maskXMaxMove;
  const bixImgYMove = (maskY * bigImgYMaxMove) / maskYMaxMove;
  bigImg.value.style.left = bixImgXMove + 'px';
  bigImg.value.style.top = bixImgYMove + 'px';
};
// 让imgSrc等于imageArray的第一项


const imgSrc = ref('');
onMounted(async () => {
  await nextTick();
  imgSrc.value = props.imageArray[0];
});
function changeImg(item) {
  imgSrc.value = item;
}
</script>

<template>
  <div class="magnify-container">
    <div class="tb-booth" @mouseenter="onMouseEnter" @mouseleave="onMouseLeave" @mousemove="onMouseMove" ref="boothRef">
      <img class="small-img" :src="imgSrc" />
      <div class="mask" ref="mask" v-show="state.boxShow" />
      <div class="big-img_box" ref="bigImgBox" v-show="state.boxShow">
        <img class="big-img" ref="bigImg" :src="imgSrc" />
      </div>
    </div>

    <div class="img-wraper">
      <img v-for="item in props.imageArray" :key="item" :src="item" @mouseenter="changeImg(item)">
    </div>
  </div>
</template>

<style lang="scss" scoped>
.magnify-container {
  display: flex;
  width: 580px;
  height: 460px;
  padding: 30px 50px;

  .tb-booth {
    width: 400px;
    height: 400px;
    background: #f5f5f5;
    cursor: move;
    position: relative;
    border: 1px solid #cccccc;
    display: flex;

    .small-img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .mask {
      position: absolute;
      top: 0;
      left: 0;
      width: 200px;
      height: 200px;
      background-color: rgba(0, 0, 0, .7);
      opacity: 0.5;
      cursor: move;
    }

    .big-img_box {
      position: absolute;
      top: 0;
      left: 400px;
      width: 400px;
      height: 400px;
      background-color: #fff;
      border: 1px solid #cccccc;
      overflow: hidden;
      z-index: 10;
    }

    .big-img {
      position: absolute;
      left: 0;
      top: 0;
    }
  }

  .img-wraper {
    margin-left: 20px;
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    height: 400px;

    img {
      width: 68px;
      height: 68px;

      &:hover {
        cursor: pointer;
        border: 1px solid #27ba9b;
      }
    }
  }
}
</style>
