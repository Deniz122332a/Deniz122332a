<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Araba Oyunu</title>
    <style>
        /* Sayfa genel stil ayarları */
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            overflow: hidden; /* Sayfa taşmalarını engeller */
            background-color: #f0f0f0;
        }

        /* Oyun alanı (container) */
        #gameContainer {
            position: relative;
            width: 100%;
            height: 100vh;
            overflow: hidden;
            background-color: #000;
        }

        /* Araba */
        #car {
            position: absolute;
            width: 50px;
            height: 100px;
            background-color: red;
            bottom: 10px;
            left: 50%;
            margin-left: -25px;
        }

        /* Puan gösterge paneli */
        #scoreboard {
            position: absolute;
            top: 10px;
            left: 10px;
            font-size: 24px;
            color: white;
        }

        /* Oyun bitiş mesajı */
        #gameOver {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            display: none; /* Başlangıçta görünmez */
        }

        /* Kontrol butonları */
        #buttons {
            position: absolute;
            bottom: 20px;
            left: 50%;
            transform: translateX(-50%);
            display: flex;
            justify-content: space-between;
            width: 50%;
        }

        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 15px;
            text-align: center;
            font-size: 16px;
            cursor: pointer;
            border-radius: 5px;
            width: 70px;
        }

        button:hover {
            background-color: #45a049;
        }

        #restartButton {
            background-color: #f44336;
        }

        #restartButton:hover {
            background-color: #e53935;
        }
    </style>
</head>
<body>
    <div id="gameContainer">
        <div id="car"></div>
        <div id="scoreboard">
            <span>Puan: <span id="score">0</span></span>
        </div>

        <div id="gameOver">
            <h2>Oyun Bitti!</h2>
            <p>Puanınız: <span id="finalScore">0</span></p>
            <button id="restartButton">Tekrar Başlat</button>
        </div>

        <div id="buttons">
            <button id="leftButton">Sola</button>
            <button id="rightButton">Sağa</button>
            <button id="accelerateButton">Gaz</button>
            <button id="brakeButton">Fren</button>
        </div>
    </div>

    <script>
        let car = document.getElementById('car');
        let scoreDisplay = document.getElementById('score');
        let gameOverDisplay = document.getElementById('gameOver');
        let finalScoreDisplay = document.getElementById('finalScore');
        let restartButton = document.getElementById('restartButton');
        let score = 0;
        let speed = 0;
        let gameInterval;
        let obstacles = [];
        let carPosition = 50; // Arabanın başlangıç pozisyonu

        // Arabayı sola hareket ettir
        document.getElementById('leftButton').addEventListener('click', function() {
            if (carPosition > 0) {
                carPosition -= 5;
                car.style.left = carPosition + '%';
            }
        });

        // Arabayı sağa hareket ettir
        document.getElementById('rightButton').addEventListener('click', function() {
            if (carPosition < 100) {
                carPosition += 5;
                car.style.left = carPosition + '%';
            }
        });

        // Gaz pedalını çalıştır (Hızlan)
        document.getElementById('accelerateButton').addEventListener('click', function() {
            speed += 1;
            if (speed > 10) speed = 10; // Hızı sınırlamak için
        });

        // Fren pedalını çalıştır (Yavaşlat)
        document.getElementById('brakeButton').addEventListener('click', function() {
            speed -= 1;
            if (speed < 0) speed = 0; // Hızı negatif yapmamak için
        });

        // Oyun başlatma
        function startGame() {
            score = 0;
            speed = 0;
            carPosition = 50; // Araba başlangıçta ortada
            car.style.left = carPosition + '%';
            scoreDisplay.textContent = score;

            gameOverDisplay.style.display = 'none';

            gameInterval = setInterval(function() {
                moveObstacles();
                generateObstacles();
                updateScore();
            }, 1000);
        }

        // Engelleri hareket ettir
        function moveObstacles() {
            let carRect = car.getBoundingClientRect();
            for (let i = 0; i < obstacles.length; i++) {
                obstacles[i].top += speed;
                let obstacleRect = obstacles[i].element.getBoundingClientRect();
                if (obstacleRect.top > window.innerHeight) {
                    obstacles[i].element.remove();
                    obstacles.splice(i, 1);
                    i--;
                    continue;
                }

                if (checkCollision(carRect, obstacleRect)) {
                    endGame();
                }
            }
        }

        // Engelleri oluştur
        function generateObstacles() {
            if (Math.random() < 0.1) {
                let obstacle = document.createElement('div');
                obstacle.classList.add('obstacle');
                obstacle.style.position = 'absolute';
                obstacle.style.width = '50px';
                obstacle.style.height = '50px';
                obstacle.style.backgroundColor = 'blue';
                obstacle.style.left = Math.random() * 100 + '%';
                obstacle.style.top = '-50px';
                document.getElementById('gameContainer').appendChild(obstacle);
                obstacles.push({element: obstacle, top: 0});
            }
        }

        // Çarpışmayı kontrol et
        function checkCollision(carRect, obstacleRect) {
            return !(carRect.right < obstacleRect.left ||
                carRect.left > obstacleRect.right ||
                carRect.bottom < obstacleRect.top ||
                carRect.top > obstacleRect.bottom);
        }

        // Puan güncelleme
        function updateScore() {
            score += 1;
            scoreDisplay.textContent = score;
        }

        // Oyunu bitir
        function endGame() {
            clearInterval(gameInterval);
            finalScoreDisplay.textContent = score;
            gameOverDisplay.style.display = 'block';
        }

        // Yeniden başlat
        restartButton.addEventListener('click', function() {
            document.getElementById('gameContainer').innerHTML = "";
            startGame();
        });

        startGame(); // Oyunu başlat
    </script>
</body>
</html>
