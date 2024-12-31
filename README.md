<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>2D Car Game</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background-color: #333;
    }
    canvas {
      display: block;
      margin: 0 auto;
      background-color: #555;
    }
    #restartButton {
      display: none;
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      padding: 10px 20px;
      font-size: 18px;
      background-color: #007BFF;
      color: white;
      border: none;
      border-radius: 5px;
      cursor: pointer;
    }
    #restartButton:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <button id="restartButton">Restart</button>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const restartButton = document.getElementById('restartButton');

    canvas.width = 400;
    canvas.height = 600;

    const car = {
      x: canvas.width / 2 - 20,
      y: canvas.height - 100,
      width: 40,
      height: 70,
      speed: 5
    };

    const obstacles = [];
    const powerUps = [];
    const obstacleWidth = 50;
    const obstacleHeight = 70;
    const powerUpSize = 30;
    let frame = 0;
    let score = 0;
    let gameOver = false;
    let powerUpActive = false;

    function drawCar() {
      ctx.fillStyle = powerUpActive ? 'blue' : 'red';
      ctx.fillRect(car.x, car.y, car.width, car.height);
    }

    function drawObstacles() {
      ctx.fillStyle = 'black';
      obstacles.forEach(obs => {
        ctx.fillRect(obs.x, obs.y, obstacleWidth, obstacleHeight);
      });
    }

    function drawPowerUps() {
      ctx.fillStyle = 'blue';
      powerUps.forEach(pu => {
        ctx.fillRect(pu.x, pu.y, powerUpSize, powerUpSize);
      });
    }

    function drawScore() {
      ctx.fillStyle = 'white';
      ctx.font = '20px Arial';
      ctx.fillText(`Score: ${score}`, 10, 30);
    }

    function updateCar() {
      if (keys.ArrowLeft && car.x > 0) {
        car.x -= car.speed;
      }
      if (keys.ArrowRight && car.x + car.width < canvas.width) {
        car.x += car.speed;
      }
    }

    function updateObstacles() {
      if (frame % 100 === 0) {
        const xPosition = Math.random() * (canvas.width - obstacleWidth);
        obstacles.push({
          x: xPosition,
          y: -obstacleHeight
        });
      }

      obstacles.forEach((obs, index) => {
        obs.y += 3;

        // Remove obstacles that leave the canvas
        if (obs.y > canvas.height) {
          obstacles.splice(index, 1);
          score++;
        }

        // Check for collision
        if (!powerUpActive &&
          car.x < obs.x + obstacleWidth &&
          car.x + car.width > obs.x &&
          car.y < obs.y + obstacleHeight &&
          car.y + car.height > obs.y
        ) {
          gameOver = true;
          restartButton.style.display = 'block';
        } else if (
          powerUpActive &&
          car.x < obs.x + obstacleWidth &&
          car.x + car.width > obs.x &&
          car.y < obs.y + obstacleHeight &&
          car.y + car.height > obs.y
        ) {
          powerUpActive = false; 
          obstacles.splice(index, 1);
        }
      });
    }

    function updatePowerUps() {
      if (frame % 500 === 0) {
        const xPosition = Math.random() * (canvas.width - powerUpSize);
        powerUps.push({
          x: xPosition,
          y: -powerUpSize
        });
      }

      powerUps.forEach((pu, index) => {
        pu.y += 3;

        // Remove power-ups that leave the canvas
        if (pu.y > canvas.height) {
          powerUps.splice(index, 1);
        }

        // Check if car collects the power-up
        if (
          car.x < pu.x + powerUpSize &&
          car.x + car.width > pu.x &&
          car.y < pu.y + powerUpSize &&
          car.y + car.height > pu.y
        ) {
          powerUpActive = true;
          powerUps.splice(index, 1);
        }
      });
    }

    function gameLoop() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      if (gameOver) {
        ctx.fillStyle = 'red';
        ctx.font = '30px Arial';
        ctx.fillText('Game Over', canvas.width / 2 - 75, canvas.height / 2);
        ctx.fillText(`Score: ${score}`, canvas.width / 2 - 50, canvas.height / 2 + 40);
        return;
      }

      drawCar();
      drawObstacles();
      drawPowerUps();
      drawScore();
      updateCar();
      updateObstacles();
      updatePowerUps();

      frame++;
      requestAnimationFrame(gameLoop);
    }

    function resetGame() {
      car.x = canvas.width / 2 - 20;
      car.y = canvas.height - 100;
      obstacles.length = 0;
      powerUps.length = 0;
      frame = 0;
      score = 0;
      gameOver = false;
      powerUpActive = false;
      restartButton.style.display = 'none';
      gameLoop();
    }

    restartButton.addEventListener('click', resetGame);

    const keys = {};
    window.addEventListener('keydown', (e) => {
      keys[e.key] = true;
    });

    window.addEventListener('keyup', (e) => {
      keys[e.key] = false;
    });

    gameLoop();
  </script>
</body>
</html>
