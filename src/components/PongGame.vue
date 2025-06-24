<template>
  <canvas ref="canvas" width="480" height="320" class="bg-black"></canvas>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { INITIAL_LIVES } from '../constants'

const canvas = ref<HTMLCanvasElement | null>(null)
let lives = INITIAL_LIVES

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

  const resetBall = () => {
    ballX = width / 2
    ballY = height / 2
    ballVX = 2
    ballVY = 2
  }

  const update = () => {
    ctx.clearRect(0, 0, width, height)

    // ball
    ballX += ballVX
    ballY += ballVY

    if (ballX < 0 || ballX > width) ballVX *= -1
    if (ballY < 0) ballVY *= -1

    // bottom collision
    if (ballY + 5 >= height - 20) {
      if (ballX >= paddleX && ballX <= paddleX + paddleWidth) {
        ballVY *= -1
        ballY = height - 20 - 5
      } else if (ballY + 5 > height) {
        lives--
        resetBall()
      }
    }

    ctx.fillStyle = 'white'
    ctx.fillRect(ballX - 5, ballY - 5, 10, 10)

    // paddle
    ctx.fillRect(paddleX, height - 20, paddleWidth, paddleHeight)

    // display lives
    ctx.fillText(`Lives: ${lives}`, 10, 10)

    requestAnimationFrame(update)
  }

  update()

  canvas.value!.addEventListener('mousemove', (e) => {
    const rect = canvas.value!.getBoundingClientRect()
    paddleX = e.clientX - rect.left - paddleWidth / 2
  })
})
</script>
