# Corvit-Git
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        body {
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            background-color: #f4f4f9;
        }

        canvas {
            background-color: #000;
        }

        .scoreboard {
            font-family: Arial, sans-serif;
            font-size: 20px;
            color: #333;
            position: absolute;
            top: 20px;
        }
    </style>
</head>
<body>
    <div class="scoreboard">
        <p id="score">Score: 0</p>
    </div>
    <canvas id="gameCanvas" width="400" height="400"></canvas>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scale = 20;
        const rows = canvas.height / scale;
        const columns = canvas.width / scale;

        let score = 0;
        let snake;
        let food;

        // Initialize game
        function init() {
            snake = new Snake();
            food = new Food();
            food.randomize();
            score = 0;
            window.clearInterval(gameLoop);
            gameLoop = window.setInterval(game, 100);
        }

        // Snake constructor
        function Snake() {
            this.body = [{ x: 5, y: 5 }];
            this.direction = 'RIGHT';
            this.nextDirection = 'RIGHT';

            this.move = function () {
                const head = { ...this.body[0] };

                if (this.direction === 'RIGHT') head.x++;
                if (this.direction === 'LEFT') head.x--;
                if (this.direction === 'UP') head.y--;
                if (this.direction === 'DOWN') head.y++;

                this.body.unshift(head);
                if (head.x === food.x && head.y === food.y) {
                    score += 10;
                    food.randomize();
                } else {
                    this.body.pop();
                }
            };

            this.changeDirection = function (event) {
                if (event.key === 'ArrowUp' && this.direction !== 'DOWN') {
                    this.nextDirection = 'UP';
                } else if (event.key === 'ArrowDown' && this.direction !== 'UP') {
                    this.nextDirection = 'DOWN';
                } else if (event.key === 'ArrowLeft' && this.direction !== 'RIGHT') {
                    this.nextDirection = 'LEFT';
                } else if (event.key === 'ArrowRight' && this.direction !== 'LEFT') {
                    this.nextDirection = 'RIGHT';
                }
            };

            this.update = function () {
                this.direction = this.nextDirection;
                this.move();
            };

            this.draw = function () {
                ctx.fillStyle = '#00FF00';
                for (let i = 0; i < this.body.length; i++) {
                    ctx.fillRect(this.body[i].x * scale, this.body[i].y * scale, scale, scale);
                }
            };

            this.collidesWithWalls = function () {
                const head = this.body[0];
                return head.x < 0 || head.x >= columns || head.y < 0 || head.y >= rows;
            };

            this.collidesWithSelf = function () {
                const head = this.body[0];
                for (let i = 1; i < this.body.length; i++) {
                    if (head.x === this.body[i].x && head.y === this.body[i].y) {
                        return true;
                    }
                }
                return false;
            };
        }

        // Food constructor
        function Food() {
            this.x;
            this.y;

            this.randomize = function () {
                this.x = Math.floor(Math.random() * columns);
                this.y = Math.floor(Math.random() * rows);
            };

            this.draw = function () {
                ctx.fillStyle = '#FF0000';
                ctx.fillRect(this.x * scale, this.y * scale, scale, scale);
            };
        }

        // Game loop
        function game() {
            snake.update();
            if (snake.collidesWithWalls() || snake.collidesWithSelf()) {
                init();
            }

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            snake.draw();
            food.draw();

            document.getElementById('score').textContent = `Score: ${score}`;
        }

        // Event listener for keyboard input
        document.addEventListener('keydown', function (event) {
            snake.changeDirection(event);
        });

        // Start the game
        init();
    </script>
</body>
</html>
