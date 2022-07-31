<template>
  <div class="in" @click="open('打开里面最外层')">
    <div class="in-dialog" @click.stop="open('打开里面的弹窗')">我是里面的</div>
  </div>
  <div class="out" @click="open('打开外面最外层')">
    <div class="out-img" @click.stop="open('打开外面的弹窗')">
      <img
        id="scream"
        class="scream"
        src="https://img1.baidu.com/it/u=1153541934,2554260511&fm=253&fmt=auto&app=138&f=JPEG?w=518&h=500"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
const open = (msg) => {
  alert(msg)
}
onMounted(() => {
  // setInterval(() => {
  //   calcPolygon(
  //     document.getElementsByClassName('in-dialog')[0],
  //     document.getElementsByClassName('out')[0]
  //   )
  // }, 11990)
  observable(document.getElementsByClassName('in-dialog')[0])
})
function calcPolygon(diffSelector, targetSelector) {
  const { bottom, left, right, top } = diffSelector.getBoundingClientRect()
  const start = ['0 0', '0 100%']
  const ceter = [
    `${left - 1}px ${top - 1}px`,
    `${right + 1}px ${top - 1}px`,
    `${right + 1}px ${bottom + 1}px`,
    `${left - 1}px ${bottom + 1}px`,
    `${left - 1}px ${top - 1}px`
  ]
  const end = ['0 100%', '100% 100%', '100% 0']
  const polygon = [...start, ...ceter, ...end].join(',')
  targetSelector.style.cssText += `clip-path: polygon(${polygon})`
}

function observable(targetNode) {
  console.log(targetNode)

  // 观察器的配置（需要观察什么变动）
  const config = { attributes: true, childList: true, subtree: true }

  // 当观察到变动时执行的回调函数
  const callback = function (mutationsList, observer) {
    console.log(mutationsList)
    // Use traditional 'for loops' for IE 11
    for (let mutation of mutationsList) {
      if (mutation.type === 'childList') {
        console.log('A child node has been added or removed.')
      } else if (mutation.type === 'attributes') {
        console.log('The ' + mutation.attributeName + ' attribute was modified.')
      }
    }
  }

  // 创建一个观察器实例并传入回调函数
  const observer = new MutationObserver(callback)

  // 以上述配置开始观察目标节点
  observer.observe(targetNode, config)
}
</script>

<style scoped lang="less">
.in {
  width: 100vw;
  height: 100vh;
  position: absolute;
  z-index: 1;
  background-color: green;

  &-dialog {
    width: 150px;
    height: 150px;
    position: absolute;
    border: 1px solid #000;
    // top: 50%;
    // left: 50%;
    background-color: red;
    animation: mymove 8s linear infinite;
  }
}

.out {
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0px;
  left: 0px;
  z-index: 2;
  pointer-events: none;
  & > * {
    pointer-events: all;
  }
  &-img {
    position: absolute;
    top: 100px;
    left: 100px;
    z-index: 2;
  }
}
@keyframes mymove {
  0% {
    left: 50px;
    top: 50px;
  }
  50% {
    left: 500px;
    top: 50px;
  }

  75% {
    left: 500px;
    top: 100px;
  }

  100% {
    left: 50px;
    top: 100px;
  }
}
</style>
