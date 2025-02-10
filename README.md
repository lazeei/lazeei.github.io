# lazeei.github.io

<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <!-- Ensure the canvas scales nicely on mobile devices -->
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Brick Breaker Game</title>
  <style>
    body {
      margin: 0;
      padding: 0;
      background: #f0f0f0;
      display: flex;
      align-items: center;
      justify-content: center;
      height: 100vh;
    }
    canvas {
      background: #eee;
      border: 2px solid #333;
      /* Note: touch-action might not be fully supported on all iOS versions */
      touch-action: none;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <script>
    const canvas = document.getElementById("gameCanvas");
    const context = canvas.getContext("2d");

    canvas.width = window.innerWidth * 0.95;
    canvas.height = window.innerHeight * 0.95;

    // Ball properties
    let ballRadius = 8;
    let ballX = canvas.width / 2;
    let ballY = canvas.height - 30;
    let ballDX = 2;
    let ballDY = -2;

    // Paddle properties
    let paddleHeight = 10;
    let paddleWidth = canvas.width * 0.2;
    let paddleX = (canvas.width - paddleWidth) / 2;

    // Brick properties
    const brickRowCount = 5;
    const brickColumnCount = 6;
    const brickPadding = 10;
    const brickOffsetTop = 30;
    const brickOffsetLeft = 10;
    const brickWidth = (canvas.width - brickOffsetLeft * 2 - brickPadding * (brickColumnCount - 1)) / brickColumnCount;
    const brickHeight = 15;

    let bricks = [];
    for (let c = 0; c < brickColumnCount; c++) {
      bricks[c] = [];
      for (let r = 0; r < brickRowCount; r++) {
        bricks[c][r] = { x: 0, y: 0, status: 1 };
      }
    }

    let score = 0;
    let lives = 3;

    function drawBricks() {
      for (let c = 0; c < brickColumnCount; c++) {
        for (let r = 0; r < brickRowCount; r++) {
          if (bricks[c][r].status === 1) {
            let brickX = c * (brickWidth + brickPadding) + brickOffsetLeft;
            let brickY = r * (brickHeight + brickPadding) + brickOffsetTop;
            bricks[c][r].x = brickX;
            bricks[c][r].y = brickY;
            context.beginPath();
            context.rect(brickX, brickY, brickWidth, brickHeight);
            context.fillStyle = "#0095DD";
            context.fill();
            context.closePath();
          }
        }
      }
    }

    function drawBall() {
      context.beginPath();
      context.arc(ballX, ballY, ballRadius, 0, Math.PI * 2);
      context.fillStyle = "#0095DD";
      context.fill();
      context.closePath();
    }

    function drawPaddle() {
      context.beginPath();
      context.rect(paddleX, canvas.height - paddleHeight, paddleWidth, paddleHeight);
      context.fillStyle = "#0095DD";
      context.fill();
      context.closePath();
    }

    function collisionDetection() {
      for (let c = 0; c < brickColumnCount; c++) {
        for (let r = 0; r < brickRowCount; r++) {
          let b = bricks[c][r];
          if (b.status === 1) {
            if (ballX > b.x && ballX < b.x + brickWidth && ballY > b.y && ballY < b.y + brickHeight) {
              ballDY = -ballDY;
              b.status = 0;
              score++;
              if (score === brickRowCount * brickColumnCount) {
                alert("Congratulations! You won!");
                document.location.reload();
              }
            }
          }
        }
      }
    }

    function drawScore() {
      context.font = "16px Arial";
      context.fillStyle = "#000";
      context.fillText("Score: " + score, 8, 20);
    }

    function drawLives() {
      context.font = "16px Arial";
      context.fillStyle = "#000";
      context.fillText("Lives: " + lives, canvas.width - 80, 20);
    }

    function handleTouchMove(e) {
      e.preventDefault();
      if (e.touches.length > 0) {
        const touch = e.touches[0];
        const rect = canvas.getBoundingClientRect();
        const relativeX = touch.clientX - rect.left;
        paddleX = relativeX - paddleWidth / 2;
        if (paddleX < 0) {
          paddleX = 0;
        } else if (paddleX + paddleWidth > canvas.width) {
          paddleX = canvas.width - paddleWidth;
        }
      }
    }

    // Use the {passive: false} option to ensure preventDefault() works on touch events
    canvas.addEventListener("touchmove", handleTouchMove, { passive: false });

    function draw() {
      context.clearRect(0, 0, canvas.width, canvas.height);

      drawBricks();
      drawBall();
      drawPaddle();
      drawScore();
      drawLives();

      collisionDetection();

      if (ballX + ballDX > canvas.width - ballRadius || ballX + ballDX < ballRadius) {
        ballDX = -ballDX;
      }
      if (ballY + ballDY < ballRadius) {
        ballDY = -ballDY;
      } else if (ballY + ballDY > canvas.height - ballRadius) {
        if (ballX > paddleX && ballX < paddleX + paddleWidth) {
          ballDY = -ballDY;
        } else {
          lives--;
          if (!lives) {
            alert("Game Over");
            document.location.reload();
          } else {
            ballX = canvas.width / 2;
            ballY = canvas.height - 30;
            ballDX = 2;
            ballDY = -2;
            paddleX = (canvas.width - paddleWidth) / 2;
          }
        }
      }

      ballX += ballDX;
      ballY += ballDY;

      requestAnimationFrame(draw);
    }

    draw();
  </script>
</body>
</html>
