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
  calcPolygon(
    document.getElementsByClassName('in-dialog')[0],
    document.getElementsByClassName('out')[0]
  )
  setTimeout(() => {
    document.getElementsByClassName('in-dialog')[0].style.cssText += `left:130px`
  }, 3000)
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
  const config = { attributes: true, childList: false, subtree: false }

  // 当观察到突变时执行的回调函数
  var callback = function (mutationsList) {
    console.log(mutationsList, 'mutationsList')
    mutationsList.forEach(function (item, index) {
      if (item.type == 'childList') {
        console.log('有节点发生改变')
      } else if (item.type == 'attributes') {
        calcPolygon(
          document.getElementsByClassName('in-dialog')[0],
          document.getElementsByClassName('out')[0]
        )
      } else if (item.type == 'subtree') {
        console.log('subtree有变化')
      }
    })
  }

  // 创建一个链接到回调函数的观察者实例
  var observer = new MutationObserver(callback)

  // 开始观察已配置突变的目标节点
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
    // top: 50%;
    // left: 50%;
    background-color: red;
    // animation: mymove 8s linear infinite;
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
