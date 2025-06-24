<template>
  <canvas ref="canvas" width="480" height="320" class="bg-black"></canvas>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'

const canvas = ref<HTMLCanvasElement | null>(null)

onMounted(() => {
  const ctx = canvas.value!.getContext('2d')!
  const width = canvas.value!.width
  const height = canvas.value!.height

  let ballX = width / 2
  let ballY = height / 2
  let ballVX = 2
  let ballVY = 2
  const paddleWidth = 60
  const paddleHeight = 10
  let paddleX = width / 2 - paddleWidth / 2

  const update = () => {
    ctx.clearRect(0, 0, width, height)

    // ball
    ballX += ballVX
    ballY += ballVY

    if (ballX < 0 || ballX > width) ballVX *= -1
    if (ballY < 0 || ballY > height) ballVY *= -1

    ctx.fillStyle = 'white'
    ctx.fillRect(ballX - 5, ballY - 5, 10, 10)

    // paddle
    ctx.fillRect(paddleX, height - 20, paddleWidth, paddleHeight)

    requestAnimationFrame(update)
  }

  update()

  canvas.value!.addEventListener('mousemove', (e) => {
    const rect = canvas.value!.getBoundingClientRect()
    paddleX = e.clientX - rect.left - paddleWidth / 2
  })
})
</script>
