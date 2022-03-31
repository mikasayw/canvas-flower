<template>
  <canvas ref="element" class="canvas" />
</template>

<script lang="ts" setup>
const element = $ref<HTMLCanvasElement>()
const ctx = $computed(() => element!.getContext('2d')!)
const WIDTH = document.body.clientWidth * 2
const HEIGHT = document.body.clientHeight * 2
interface Branch {
  start: Point
  length: number
  angle: number
}

interface Point {
  x: number
  y: number
}

onMounted(() => {
  element.width = WIDTH
  element.height = HEIGHT
  init()
})

function init() {
  ctx.strokeStyle = 'rgba(107,114,128,.3)'

  const startPoint: Branch = {
    start: { x: WIDTH / 2, y: HEIGHT },
    length: 2,
    angle: -Math.PI / 2,
  }

  step(startPoint)
}
const pendingTask: Array<Function> = []

function step(b: Branch, depth = 0) {
  drawBranch(b)
  const end = getEndPoint(b)

  if (depth < 5 || Math.random() < 0.5) {
    pendingTask.push(() => {
      step({
        start: end,
        length: 2 + Math.random() * 10 - 5,
        angle: b.angle + Math.random() * 0.3,
      }, depth + 1)
    })
  }
  if (depth < 5 || Math.random() < 0.5) {
    pendingTask.push(() => {
      step({
        start: end,
        length: 2 + Math.random() * 10 - 5,
        angle: b.angle - Math.random() * 0.3,
      }, depth + 1)
    })
  }
}

function frame() {
  const tasks = [...pendingTask]
  pendingTask.length = 0
  tasks.forEach(fn => fn())
}

let frameCount = 0
function loopFrame() {
  requestAnimationFrame(() => {
    frameCount += 2
    if (frameCount % 6 === 0)
      frame()

    loopFrame()
  })
}
loopFrame()

function getEndPoint(b: Branch) {
  const { start, length, angle } = b
  return {
    x: start.x + length * Math.cos(angle),
    y: start.y + length * Math.sin(angle),
  }
}

function drawBranch(b: Branch) {
  lineTo(b.start, getEndPoint(b))
}

function lineTo(p1: Point, p2: Point) {
  ctx.beginPath()
  ctx.moveTo(p1.x + 0.1, p1.y + 0.1)
  ctx.lineTo(p2.x + 0.1, p2.y + 0.1)
  ctx.stroke()
}

</script>

<style scoped>
html {
  height: 100%;
}
body {
  margin: 0;
  height: 100%;
  background: #fff;
}
.canvas {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}
</style>
