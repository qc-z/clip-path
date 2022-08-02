<template>
  <div class="in" @click.stop="open('打开里面的外层')">
    <div class="in-dialog" ref="el" :style="style" @click.stop="open('打开里面弹窗')"></div>
  </div>
  <div class="out" @click.stop="open('打开外面的外层')">
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
import { useDraggable } from '@vueuse/core'
const open = (msg) => {
  console.log(msg)
}
const el = ref<HTMLElement | null>(null)

const { x, y, style } = useDraggable(el)
onMounted(() => {
  const target = <HTMLImageElement>document.getElementsByClassName('in-dialog')[0]
  const box = document.getElementsByClassName('out')[0]
  observable(target)
  setInterval(() => {
    target.style.cssText += `left: ${target.offsetLeft + 1}px`
  }, 100)
})
function calcPolygon(target, box) {
  const { bottom, left, right, top } = target.getBoundingClientRect()
  const start = ['0 0']
  const ceter = [
    `${left}px ${top}px`,
    `${right}px ${top}px`,
    `${right}px ${bottom}px`,
    `${left}px ${bottom}px`,
    `${left}px ${top}px`
  ]
  const end = ['0 0', '0 100%', '100% 100%', '100% 0']
  const polygon = [...start, ...ceter, ...end].join(',')
  box.style.cssText += `clip-path: polygon(${polygon})`
}
function setStyle(target) {
  const { bottom, left, right, top } = target.getBoundingClientRect()
  const polygon = [
    `${left}px ${top}px`,
    `${right}px ${top}px`,
    `${right}px ${bottom}px`,
    `${left}px ${bottom}px`,
    `${left}px ${top}px`
  ]
  return polygon
}

function observable(targetNode) {
  console.log(111)
  calcPolygon(
    document.getElementsByClassName('in-dialog')[0],
    document.getElementsByClassName('out')[0]
  )
  // 观察器的配置（需要观察什么变动）
  const config = {
    attributes: true,
    childList: true,
    subtree: true,
    attributeOldValue: true,
    characterDataOldValue: true
  }

  // 当观察到突变时执行的回调函数
  var callback = function (mutationsList) {
    console.log(mutationsList)
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
  width: 80vw;
  height: 80vh;
  position: absolute;
  z-index: 1;
  background-color: green;

  &-dialog {
    width: 150px;
    height: 150px;
    // border-radius: 50%;
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
    left: 0px;
  }
  50% {
    left: 100px;
  }

  75% {
    left: 200px;
  }

  100% {
    left: 300px;
  }
}
</style>
