<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>XP Hero Game</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
      background-color: #2a2a2a;
      color: white;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      overflow: hidden;
    }
    canvas {
      border: 2px solid #fff;
      background-color: #1a1a1a;
    }
    .game-info {
      position: absolute;
      top: 10px;
      left: 10px;
      background: rgba(0, 0, 0, 0.7);
      padding: 10px;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <div class="game-info">
    <p>Score: <span id="score">0</span></p>
    <p>Level: <span id="level">1</span></p>
  </div>
  <canvas id="gameCanvas" width="400" height="600"></canvas>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    let score = 0;
    let level = 1;
    let gameSpeed = 5;

    const player = {
      x: 180,
      y: 500,
      width: 40,
      height: 40,
      color: 'blue',
      dx: 0,
    };

    const enemies = [];
    const bullets = [];

    function drawPlayer() {
      ctx.fillStyle = player.color;
      ctx.fillRect(player.x, player.y, player.width, player.height);
    }

    function movePlayer() {
      player.x += player.dx;
      if (player.x < 0) player.x = 0;
      if (player.x + player.width > canvas.width) player.x = canvas.width - player.width;
    }

    function spawnEnemies() {
      if (Math.random() < 0.02) {
        const enemy = {
          x: Math.random() * (canvas.width - 40),
          y: -40,
          width: 40,
          height: 40,
          color: 'red',
        };
        enemies.push(enemy);
      }
    }

    function drawEnemies() {
      enemies.forEach((enemy, index) => {
        ctx.fillStyle = enemy.color;
        ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);

        enemy.y += gameSpeed;

        if (enemy.y > canvas.height) {
          enemies.splice(index, 1);
        }

        if (
          player.x < enemy.x + enemy.width &&
          player.x + player.width > enemy.x &&
          player.y < enemy.y + enemy.height &&
          player.y + player.height > enemy.y
        ) {
          alert('Game Over! Your Score: ' + score);
          document.location.reload();
        }
      });
    }

    function shootBullet() {
      const bullet = {
        x: player.x + player.width / 2 - 5,
        y: player.y,
        width: 10,
        height: 20,
        color: 'yellow',
      };
      bullets.push(bullet);
    }

    function drawBullets() {
      bullets.forEach((bullet, index) => {
        ctx.fillStyle = bullet.color;
        ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);

        bullet.y -= 10;

        if (bullet.y < 0) {
          bullets.splice(index, 1);
        }
      });
    }

    function checkCollisions() {
      bullets.forEach((bullet, bIndex) => {
        enemies.forEach((enemy, eIndex) => {
          if (
            bullet.x < enemy.x + enemy.width &&
            bullet.x + bullet.width > enemy.x &&
            bullet.y < enemy.y + enemy.height &&
            bullet.y + bullet.height > enemy.y
          ) {
            bullets.splice(bIndex, 1);
            enemies.splice(eIndex, 1);
            score += 10;
            document.getElementById('score').innerText = score;

            if (score % 100 === 0) {
              level++;
              document.getElementById('level').innerText = level;
              gameSpeed += 1;
            }
          }
        });
      });
    }

    function gameLoop() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      drawPlayer();
      movePlayer();
      spawnEnemies();
      drawEnemies();
      drawBullets();
      checkCollisions();

      requestAnimationFrame(gameLoop);
    }

    document.addEventListener('keydown', (e) => {
      if (e.key === 'ArrowLeft') player.dx = -5;
      if (e.key === 'ArrowRight') player.dx = 5;
      if (e.key === ' ') shootBullet();
    });

    document.addEventListener('keyup', (e) => {
      if (e.key === 'ArrowLeft' || e.key === 'ArrowRight') player.dx = 0;
    });

    gameLoop();
  </script>
</body>
</html>
