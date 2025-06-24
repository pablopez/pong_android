<template>
  <div class="relative">
    <canvas ref="canvas" width="480" height="320" class="bg-black"></canvas>
    <div
      v-if="gameOver"
      class="absolute inset-0 flex flex-col items-center justify-center bg-black bg-opacity-75 text-white"
    >
      <div class="mb-2 text-2xl">Game Over</div>
      <button
        class="px-4 py-2 bg-white text-black rounded"
        @click="handleRestart"
      >
        Restart
      </button>
    </div>
  </div>
</template>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { INITIAL_LIVES, SPEED_INCREMENT } from '../../../shared/constants'

const canvas = ref<HTMLCanvasElement | null>(null)
let lives = INITIAL_LIVES
let score = 0
let level = 1
const gameOver = ref(false)
let handleRestart = () => {}

onMounted(() => {
  const ctx = canvas.value!.getContext('2d')!
  const width = canvas.value!.width
  const height = canvas.value!.height

  let ballX = width / 2
  let ballY = height / 2
  let ballVX = 2
  let ballVY = 2
  const baseSpeed = Math.sqrt(ballVX * ballVX + ballVY * ballVY)
  const paddleWidth = 60
  const paddleHeight = 10
  let paddleX = width / 2 - paddleWidth / 2
  let paddleDX = 0

  const resetBall = () => {
    ballX = width / 2
    ballY = height / 2
    ballVX = 2
    ballVY = 2
  }

  const update = () => {
    ctx.clearRect(0, 0, width, height)

    if (gameOver.value) {
      ctx.fillStyle = 'white'
      ctx.fillText('Game Over', width / 2 - 40, height / 2)
      return
    }

    // update paddle position from keyboard input
    paddleX += paddleDX
    if (paddleX < 0) paddleX = 0
    if (paddleX + paddleWidth > width) paddleX = width - paddleWidth

    // ball
    ballX += ballVX
    ballY += ballVY

    if (ballX < 0 || ballX > width) ballVX *= -1
    if (ballY < 0) ballVY *= -1

    // bottom collision
    if (ballY + 5 >= height - 20) {
      if (ballX >= paddleX && ballX <= paddleX + paddleWidth) {
        ballVY = -ballVY
        ballVX *= 1 + SPEED_INCREMENT
        ballVY *= 1 + SPEED_INCREMENT
        ballY = height - 20 - 5

        const speed = Math.sqrt(ballVX * ballVX + ballVY * ballVY)
        const increment = Math.max(1, Math.round(speed / baseSpeed))
        score += increment
        level = Math.max(1, Math.round(speed / baseSpeed))
      } else if (ballY + 5 > height) {
        lives--
        if (lives <= 0) {
          gameOver.value = true
        } else {
          resetBall()
        }
      }
    }

    ctx.fillStyle = 'white'
    ctx.fillRect(ballX - 5, ballY - 5, 10, 10)

    // paddle
    ctx.fillRect(paddleX, height - 20, paddleWidth, paddleHeight)

    // display stats
    ctx.fillText(`Lives: ${lives}`, 10, 10)
    ctx.fillText(`Score: ${score}`, 10, 20)
    ctx.fillText(`Level: ${level}`, 10, 30)

    requestAnimationFrame(update)
  }

  update()

  handleRestart = () => {
    lives = INITIAL_LIVES
    score = 0
    level = 1
    gameOver.value = false
    resetBall()
    update()
  }

  canvas.value!.addEventListener('mousemove', (e) => {
    const rect = canvas.value!.getBoundingClientRect()
    paddleX = e.clientX - rect.left - paddleWidth / 2
  })

  canvas.value!.addEventListener('touchmove', (e) => {
    const rect = canvas.value!.getBoundingClientRect()
    paddleX = e.touches[0].clientX - rect.left - paddleWidth / 2
    e.preventDefault()
  })

  window.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowLeft') {
      paddleDX = -5
    } else if (e.key === 'ArrowRight') {
      paddleDX = 5
    }
  })

  window.addEventListener('keyup', (e) => {
    if (e.key === 'ArrowLeft' && paddleDX < 0) {
      paddleDX = 0
    } else if (e.key === 'ArrowRight' && paddleDX > 0) {
      paddleDX = 0
    }
  })
})
</script>
