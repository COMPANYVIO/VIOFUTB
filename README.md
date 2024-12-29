<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Horarios de Fútbol</title>
    <style>
        body {   
            font-family: 'Arial', sans-serif;
            margin: 0;
            padding: 0;
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            color: #fff;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
        }

        .container {
            max-width: 800px;
            padding: 20px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 15px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
            text-align: center;
        }

        h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            color: #ffd700;
        }

        .main-title {
            font-size: 1.2rem;
            margin-bottom: 20px;
            color: #f0f0f0;
        }

        .clock-container {
            margin: 20px auto;
            padding: 15px;
            background-color: #2e8b57;
            color: #fff;
            border-radius: 10px;
            width: 70%;
        }

        .clock {
            font-size: 1.5rem;
            font-weight: bold;
        }

        .button {
            display: inline-block;
            margin-top: 20px;
            padding: 10px 20px;
            background: #ffd700;
            color: #333;
            border-radius: 5px;
            text-decoration: none;
            font-size: 1rem;
            font-weight: bold;
            transition: background 0.3s;
        }

        .button:hover {
            background: #ffa500;
        }

        /* Ventanas para los partidos */
        .partidos-container {
            margin-top: 30px;
        }

        .partido {
            background-color: rgba(255, 255, 255, 0.2);
            padding: 10px;
            margin: 10px 0;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.3);
        }

        .partido h2 {
            margin: 0;
            font-size: 1.5rem;
            color: #ffd700;
        }

        /* Estilos para el Mini Juego */
        #gameCanvas {
            border: 2px solid #fff;
            background-color: #34495e;
            margin-top: 20px;
            display: none; /* Inicialmente oculto */
            display: block;
            margin-left: auto;
            margin-right: auto;
        }

        .start-button {
            display: block;
            padding: 10px 20px;
            font-size: 18px;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            z-index: 10;
            text-align: center;
            margin-top: 20px;
        }

        .start-button:hover {
            background-color: #45a049;
        }

        /* Modal de Fin de Juego */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.7);
            justify-content: center;
            align-items: center;
        }
        .modal-content {
            background-color: #fff;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            border: 5px solid yellow;
        }
        .modal button {
            background-color: #4CAF50;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .modal button:hover {
            background-color: #45a049;
        }

        /* Estilo de los botones del modal */
        .restart-button, .close-button {
            display: inline-block;
            margin: 10px;
            padding: 10px 20px;
            background-color: #4CAF50;
            color: white;
            font-size: 16px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        .restart-button:hover, .close-button:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>Horario de Fútbol</h1>
        <div class="main-title">Partidos del fin de semana</div>

        <div class="clock-container">
            <div class="clock" id="clock">00:00 AM</div>
        </div>

        <!-- Botón para Mini Juego de Fútbol -->
        <a href="#" class="button" id="startButton">👉MINI JUEGO POR MIENTRAS ESPERAS</a>

        <a href="https://t.me/sigueme" target="_blank" class="button">Síguenos en Telegram</a>

        <!-- Contenedor de los partidos -->
        <div class="partidos-container">
            <div class="partido">
                <h2>Partido 1: Equipo A vs Equipo B</h2>
                <p>Fecha: Sábado, 12:00 PM</p>
                <p>Estadio: Estadio Nacional</p>
            </div>
            <div class="partido">
                <h2>Partido 2: Equipo C vs Equipo D</h2>
                <p>Fecha: Sábado, 3:00 PM</p>
                <p>Estadio: Estadio Municipal</p>
            </div>
            <div class="partido">
                <h2>Partido 3: Equipo E vs Equipo F</h2>
                <p>Fecha: Domingo, 6:00 PM</p>
                <p>Estadio: Estadio Olímpico</p>
            </div>
        </div>

        <!-- Canvas para el juego -->
        <canvas id="gameCanvas" width="400" height="300"></canvas>

        <!-- Modal de fin de juego -->
        <div class="modal" id="gameOverModal">
            <div class="modal-content">
                <h2>FIN DEL JUEGO</h2>
                <p id="finalScore">PUNTOS: 0</p>
                <p id="percentageScore">Porcentaje de éxito: 0%</p>
                <button class="restart-button" onclick="restartGame()">Reiniciar Juego</button>
                <button class="close-button" onclick="closeGame()">Cerrar Juego</button>
            </div>
        </div>
    </div>

    <script>
        function updateClock() {
            const now = new Date();
            let hours = now.getHours();
            let minutes = now.getMinutes();
            let seconds = now.getSeconds();
            let ampm = hours >= 12 ? 'PM' : 'AM';
            
            // Convertir las horas a formato de 12 horas
            hours = hours % 12;
            hours = hours ? hours : 12; // El 0 se convierte en 12
            minutes = minutes < 10 ? '0' + minutes : minutes;
            seconds = seconds < 10 ? '0' + seconds : seconds;

            const timeString = `${hours}:${minutes}:${seconds} ${ampm}`;
            document.getElementById('clock').textContent = timeString;
        }

        setInterval(updateClock, 1000);

        // Código del mini juego de fútbol
        const canvas = document.getElementById('gameCanvas');
        const context = canvas.getContext('2d');
        const gameOverModal = document.getElementById('gameOverModal');
        const startButton = document.getElementById('startButton');
        const finalScoreText = document.getElementById('finalScore');
        const percentageScoreText = document.getElementById('percentageScore');

        const ball = {
            x: canvas.width / 2,
            y: canvas.height / 2,
            radius: 10, // Radio de la pelota blanca
            dx: 0,
            dy: 0,
            speed: 1, // Lento
            lives: 3
        };

        const redBalls = [];
        let score = 0;
        let totalRedBalls = 0; // Total de pelotas rojas generadas
        let gameRunning = false;
        const initialLives = 3;

        // Suma un punto cada 4 segundos
        setInterval(() => {
            if (gameRunning && ball.lives > 0) {
                score++;
            }
        }, 4000);

        // Genera pelotas rojas cada 2 segundos
        setInterval(() => {
            if (gameRunning) {
                spawnRedBall();
            }
        }, 2000);

        function drawBall() {
            if (ball.lives <= 0) return;
            context.beginPath();
            context.arc(ball.x, ball.y, ball.radius, 0, Math.PI * 2);
            context.fillStyle = '#fff';
            context.fill();
            context.strokeStyle = '#000';
            context.lineWidth = 2;
            context.stroke();
            context.closePath();
        }

        function drawRedBalls() {
            for (const redBall of redBalls) {
                context.beginPath();
                context.arc(redBall.x, redBall.y, redBall.radius, 0, Math.PI * 2);
                context.fillStyle = '#f00';
                context.fill();
                context.closePath();
            }
        }

        function drawScore() {
            context.fillStyle = '#fff';
            context.font = '20px Arial';
            context.fillText('MINI SCA FUTB', canvas.width / 2 - 40, 20);
            context.font = '16px Arial';
            context.fillText(`Marcador: ${score}`, 10, canvas.height - 10);
            context.fillText(`Vidas: ${ball.lives}`, canvas.width - 100, canvas.height - 10);
        }

        function moveBall(e) {
            const rect = canvas.getBoundingClientRect();
            const mouseX = e.clientX - rect.left;
            const mouseY = e.clientY - rect.top;

            const angle = Math.atan2(mouseY - ball.y, mouseX - ball.x);
            ball.dx = Math.cos(angle) * ball.speed;
            ball.dy = Math.sin(angle) * ball.speed;
        }

        function checkCollision(ball1, ball2) {
            const dx = ball1.x - ball2.x;
            const dy = ball1.y - ball2.y;
            const distance = Math.sqrt(dx * dx + dy * dy);
            return distance < ball1.radius + ball2.radius;
        }

        function updateBall() {
            if (ball.lives <= 0) return;

            ball.x += ball.dx;
            ball.y += ball.dy;

            // La pelota blanca no se queda atrapada en las paredes
            if (ball.x - ball.radius < 0) ball.x = ball.radius;
            if (ball.x + ball.radius > canvas.width) ball.x = canvas.width - ball.radius;
            if (ball.y - ball.radius < 0) ball.y = ball.radius;
            if (ball.y + ball.radius > canvas.height) ball.y = canvas.height - ball.radius;
        }

        function updateRedBalls() {  
            for (let i = 0; i < redBalls.length; i++) {
                const redBall = redBalls[i];

                redBall.x += redBall.dx;
                redBall.y += redBall.dy;

                // Las pelotas rojas rebotan en las paredes
                if (redBall.x - redBall.radius < 0 || redBall.x + redBall.radius > canvas.width) {
                    redBall.dx = -redBall.dx;
                }

                if (redBall.y - redBall.radius < 0 || redBall.y + redBall.radius > canvas.height) {
                    redBall.dy = -redBall.dy;
                }

                if (checkCollision(ball, redBall)) {
                    ball.lives--;
                    redBalls.splice(i, 1);
                    i--;
                    if (ball.lives <= 0) {
                        showGameOver();
                    }
                }
            }
        }

        function spawnRedBall() {
            const radius = ball.radius; // Red balls have the same radius as the white ball
            const x = Math.random() * (canvas.width - radius * 2) + radius;
            const y = Math.random() * (canvas.height - radius * 2) + radius;

            // Direcciones aleatorias para las pelotas rojas
            const dx = (Math.random() - 0.5) * 2;
            const dy = (Math.random() - 0.5) * 2;

            redBalls.push({ x, y, radius, dx, dy });
        }

        function gameLoop() {
            if (!gameRunning) return;

            context.clearRect(0, 0, canvas.width, canvas.height);
            drawBall();
            drawRedBalls();
            drawScore();
            updateBall();
            updateRedBalls();

            requestAnimationFrame(gameLoop);
        }

        function showGameOver() {
            gameRunning = false;
            const successPercentage = ((score / totalRedBalls) * 100 || 0);
            finalScoreText.textContent = `PUNTOS: ${score}`;
            percentageScoreText.textContent = `Porcentaje de éxito: ${successPercentage.toFixed(2)}%`;
            gameOverModal.style.display = 'flex';
        }

        function closeGame() {
            gameOverModal.style.display = 'none';
            canvas.style.display = 'none';
            startButton.style.display = 'block';
        }

        function restartGame() {
            gameOverModal.style.display = 'none';
            canvas.style.display = 'block';
            startButton.style.display = 'none';
            startGame();
        }

        startButton.addEventListener('click', function() {
            startGame();
            startButton.style.display = 'none';
        });

        function startGame() {
            ball.lives = initialLives;
            ball.x = canvas.width / 2;
            ball.y = canvas.height / 2;
            redBalls.length = 0;
            score = 0;
            totalRedBalls = 0;
            gameRunning = true;
            canvas.style.display = 'block';
            gameOverModal.style.display = 'none';

            spawnRedBall();
            gameLoop();
        }

        canvas.addEventListener('click', moveBall);
    </script>
</body>
</html>
