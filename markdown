<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>貪食蛇</title>
<style>
* { margin: 0; padding: 0; box-sizing: border-box; }
body {
  background: #1a1a2e;
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 100vh;
  font-family: 'Segoe UI', Arial, sans-serif;
}
.game-container {
  text-align: center;
  padding: 20px;
}
h1 {
  color: #e94560;
  font-size: 2.5em;
  margin-bottom: 10px;
  text-shadow: 0 0 10px rgba(233,69,96,.5);
}
.score {
  color: #fff;
  font-size: 1.5em;
  margin-bottom: 15px;
}
.score span { color: #0f3460; background: #e94560; padding: 2px 12px; border-radius: 6px; }
canvas {
  border: 3px solid #e94560;
  border-radius: 8px;
  background: #0f3460;
  box-shadow: 0 0 20px rgba(233,69,96,.3);
  display: block;
  margin: 0 auto;
}
.controls {
  margin-top: 15px;
  color: #aaa;
  font-size: 0.9em;
}
.btn {
  background: #e94560;
  color: #fff;
  border: none;
  padding: 10px 30px;
  font-size: 1.1em;
  border-radius: 6px;
  cursor: pointer;
  margin-top: 10px;
  transition: background .2s;
}
.btn:hover { background: #c73650; }
</style>
</head>
<body>
<div class="game-container">
  <h1>貪食蛇</h1>
  <div class="score">分數：<span id="score">0</span></div>
  <canvas id="gameCanvas" width="400" height="400"></canvas>
  <div class="controls">方向鍵 / WASD 控制移動</div>
  <button class="btn" id="restartBtn">重新開始</button>
</div>
<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
const scoreEl = document.getElementById('score');
const SIZE = 20;
const COLS = canvas.width / SIZE;
const ROWS = canvas.height / SIZE;
let snake, food, direction, nextDirection, score, gameLoop;

function init() {
  snake = [{x:5,y:10},{x:4,y:10},{x:3,y:10}];
  direction = 'right';
  nextDirection = 'right';
  score = 0;
  scoreEl.textContent = '0';
  spawnFood();
}

function spawnFood() {
  do {
    food = {x:Math.floor(Math.random()*COLS),y:Math.floor(Math.random()*ROWS)};
  } while (snake.some(s=>s.x===food.x&&s.y===food.y));
}

function draw() {
  ctx.clearRect(0,0,canvas.width,canvas.height);
  snake.forEach((s,i)=>{
    ctx.fillStyle = i===0 ? '#e94560' : '#4ecca3';
    ctx.shadowBlur = i===0 ? 10 : 0;
    ctx.shadowColor = '#e94560';
    ctx.fillRect(s.x*SIZE,s.y*SIZE,SIZE-1,SIZE-1);
    ctx.shadowBlur = 0;
  });
  ctx.fillStyle = '#ffd700';
  ctx.shadowBlur = 12;
  ctx.shadowColor = '#ffd700';
  ctx.beginPath();
  ctx.arc(food.x*SIZE+SIZE/2,food.y*SIZE+SIZE/2,SIZE/2-2,0,Math.PI*2);
  ctx.fill();
  ctx.shadowBlur = 0;
}

function update() {
  direction = nextDirection;
  const head = {...snake[0]};
  switch (direction) {
    case 'up':    head.y--; break;
    case 'down':  head.y++; break;
    case 'left':  head.x--; break;
    case 'right': head.x++; break;
  }
  if (head.x<0||head.x>=COLS||head.y<0||head.y>=ROWS||snake.some(s=>s.x===head.x&&s.y===head.y)) {
    gameOver();
    return;
  }
  snake.unshift(head);
  if (head.x===food.x&&head.y===food.y) {
    score++;
    scoreEl.textContent = score;
    spawnFood();
  } else {
    snake.pop();
  }
  draw();
}

function gameOver() {
  clearInterval(gameLoop);
  ctx.fillStyle = 'rgba(0,0,0,.6)';
  ctx.fillRect(0,0,canvas.width,canvas.height);
  ctx.fillStyle = '#e94560';
  ctx.font = '40px "Segoe UI",Arial,sans-serif';
  ctx.textAlign = 'center';
  ctx.shadowBlur = 10;
  ctx.shadowColor = '#e94560';
  ctx.fillText('遊戲結束',canvas.width/2,canvas.height/2-10);
  ctx.shadowBlur = 0;
}

function startGame() {
  clearInterval(gameLoop);
  init();
  draw();
  gameLoop = setInterval(update, 180);
}

document.addEventListener('keydown', e=>{
  const key = e.key;
  if (['ArrowUp','w','W'].includes(key)&&direction!=='down') nextDirection='up';
  else if (['ArrowDown','s','S'].includes(key)&&direction!=='up') nextDirection='down';
  else if (['ArrowLeft','a','A'].includes(key)&&direction!=='right') nextDirection='left';
  else if (['ArrowRight','d','D'].includes(key)&&direction!=='left') nextDirection='right';
  if (['ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].includes(key)) e.preventDefault();
});

document.getElementById('restartBtn').addEventListener('click',startGame);

startGame();
</script>
</body>
</html>
