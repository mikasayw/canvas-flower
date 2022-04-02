<template>
  <canvas ref="element" class="canvas" :style="`width:${WIDTH/2};height:${HEIGHT/2}px;`" />
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

const startPointArray: Array<Point> = [
  { x: 0, y: HEIGHT * Math.random() },
  { x: WIDTH, y: HEIGHT * Math.random() },
  { x: WIDTH * Math.random(), y: 0 },
  { x: WIDTH * Math.random(), y: HEIGHT },
]

onMounted(() => {
  element.width = WIDTH
  element.height = HEIGHT
  init()
})

function init() {
  startPointArray.forEach((item) => {
    step(getStartPoint(item))
  })
}

function getStartPoint({ x, y }: Point): Branch {
  let _angel = Math.PI

  if (x === 0) {
    switch (y) {
      case 0:
        _angel = _angel / (3 + Math.random() * 3)
        break
      case HEIGHT:
        _angel = -_angel / (3 + Math.random() * 3)
        break
      default:
        _angel = 0
        break
    }
  }
  else if (x === WIDTH) {
    switch (y) {
      case 0:
        _angel = _angel / (1 + Math.random())
        break
      case HEIGHT:
        _angel = -_angel / (1 + Math.random())
        break
      default:
        break
    }
  }
  else {
    switch (y) {
      case 0:
        _angel /= 2
        break
      case HEIGHT:
        _angel /= -2
        break
      default:
        break
    }
  }

  return {
    start: { x, y },
    length: Math.random() * 6,
    angle: _angel,
  }
}

const pendingTask: Array<Function> = []
function step(b: Branch, depth = 0) {
  drawBranch(b)
  const end = getEndPoint(b)

  if (depth < 5 || Math.random() < 0.5) {
    pendingTask.push(() => {
      step({
        start: end,
        length: Math.random() * 10 + Math.random() * 10 - 5,
        angle: b.angle + Math.random() * 0.3,
      }, depth + 1)
    })
  }
  if (depth < 5 || Math.random() < 0.5) {
    pendingTask.push(() => {
      step({
        start: end,
        length: Math.random() * 10 + Math.random() * 10 - 5,
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

function getEndPoint(b: Branch): Point {
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
  ctx.strokeStyle = 'rgba(107,114,128,.3)'
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
}
</style>
