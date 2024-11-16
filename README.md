<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Flappy Bird with Levels</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: skyblue;
      font-family: Arial, sans-serif;
    }
    canvas {
      display: block;
      margin: 0 auto;
      background: #70c5ce; /* Sky color */
    }
  </style>
</head>
<body>
  <script>
    // Flappy Bird Game with Levels

    const canvas = document.createElement('canvas');
    const ctx = canvas.getContext('2d');
    document.body.appendChild(canvas);

    canvas.width = 320;
    canvas.height = 480;

    const bird = {
      x: 50,
      y: 150,
      width: 20,
      height: 20,
      gravity: 0.6,
      lift: -10,
      velocity: 0,
      color: [255, 0, 0] // Initial RGB color
    };

    const pipes = [];
    const pipeWidth = 40;
    let pipeGap = 120; // Gap between pipes
    let pipeSpeed = 2; // Initial speed of pipes
    let pipeInterval = 90; // Interval between pipes
    let frame = 0;
    let score = 0;
    let level = 1; // Start at level 1
    let gameRunning = true;

    function randomRGB() {
      return [Math.floor(Math.random() * 256), Math.floor(Math.random() * 256), Math.floor(Math.random() * 256)];
    }

    function drawBird() {
      if (frame % 10 === 0) {
        bird.color = randomRGB();
      }
      const [r, g, b] = bird.color;
      ctx.fillStyle = `rgb(${r}, ${g}, ${b})`;
      ctx.fillRect(bird.x, bird.y, bird.width, bird.height);
    }

    function drawPipes() {
      ctx.fillStyle = 'green';
      for (let i = 0; i < pipes.length; i++) {
        let pipe = pipes[i];
        ctx.fillRect(pipe.x, 0, pipeWidth, pipe.top);
        ctx.fillRect(pipe.x, pipe.top + pipeGap, pipeWidth, canvas.height - pipe.top - pipeGap);
      }
    }

    function updateBird() {
      bird.velocity += bird.gravity;
      bird.y += bird.velocity;

      if (bird.y + bird.height > canvas.height || bird.y < 0) {
        gameOver();
      }
    }

    function updatePipes() {
      if (frame % pipeInterval === 0) {
        const topHeight = Math.random() * (canvas.height / 2);
        pipes.push({ x: canvas.width, top: topHeight });
      }

      for (let i = 0; i < pipes.length; i++) {
        let pipe = pipes[i];
        pipe.x -= pipeSpeed;

        if (pipe.x + pipeWidth < 0) {
          pipes.splice(i, 1);
          score++;

          // Check if the level is completed
          if (score % 10 === 0) {
            levelUp();
          }
        }

        // Collision detection
        if (
          bird.x < pipe.x + pipeWidth &&
          bird.x + bird.width > pipe.x &&
          (bird.y < pipe.top || bird.y + bird.height > pipe.top + pipeGap)
        ) {
          gameOver();
        }
      }
    }

    function levelUp() {
      level++;
      pipeGap = Math.max(80, pipeGap - 5); // Reduce gap, minimum of 80
      pipeSpeed += 0.5; // Increase pipe speed
      pipeInterval = Math.max(60, pipeInterval - 5); // Reduce interval, minimum of 60

      // Display level-up message briefly
      ctx.font = '24px Arial';
      ctx.fillStyle = 'yellow';
      ctx.fillText(`Level ${level}!`, canvas.width / 4, canvas.height / 2);

      setTimeout(() => {
        if (level > 200) {
          gameRunning = false;
          alert("Congratulations! You've completed all 200 levels!");
        }
      }, 1000);
    }

    function gameOver() {
      gameRunning = false;
      ctx.font = '24px Arial';
      ctx.fillStyle = 'red';
      ctx.fillText('Game Over!', canvas.width / 4, canvas.height / 2);
      ctx.fillText(`Score: ${score}`, canvas.width / 4, canvas.height / 2 + 30);
    }

    function gameLoop() {
      if (!gameRunning) return;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      updateBird();
      updatePipes();

      drawBird();
      drawPipes();

      ctx.font = '16px Arial';
      ctx.fillStyle = 'white';
      ctx.fillText(`Score: ${score}`, 10, 20);
      ctx.fillText(`Level: ${level}`, 10, 40);

      frame++;
      requestAnimationFrame(gameLoop);
    }

    function makeBirdFlap() {
      bird.velocity = bird.lift;
    }

    document.addEventListener('keydown', (e) => {
      if (e.code === 'Space') {
        makeBirdFlap();
      }
    });

    document.addEventListener('click', () => {
      makeBirdFlap();
    });

    gameLoop();
  </script>
</body>
</html>
