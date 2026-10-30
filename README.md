<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Meu Jogo da Cobrinha</title>
    <style>
        body {
            background: #111;
            color: white;
            font-family: Arial, sans-serif;
            text-align: center;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            color: #00ff66;
            margin-bottom: 5px;
        }
        canvas {
            border: 4px solid #00ff66;
            background: #000;
            box-shadow: 0 0 20px rgba(0, 255, 102, 0.4);
        }
        p {
            color: #aaa;
        }
    </style>
</head>
<body>

    <h1>JOGO DA COBRINHA</h1>
    <p>Use as setas do teclado para mover</p>

    <canvas id="stage" width="400" height="400"></canvas>

    <script>
        const canvas = document.getElementById('stage');
        const ctx = canvas.getContext('2d');

        // Configuração do tamanho da grade e do bloco
        const tileSize = 20;
        const tileCount = 20;

        // Posição inicial da cabeça da cobra
        let snakeX = 10;
        let snakeY = 10;

        // Velocidade de movimento (direção)
        let velocityX = 0;
        let velocityY = 0;

        // Corpo da cobra (uma lista de blocos)
        let snake = [
            { x: 10, y: 10 }
        ];
        let tailLength = 2; // Tamanho inicial da cobra

        // Posição inicial da comida
        let foodX = 5;
        let foodY = 5;

        // Loop principal do jogo (roda a cada 100 milissegundos)
        let gameInterval = setInterval(gameLoop, 100);

        function gameLoop() {
            update();
            draw();
        }

        function update() {
            // Movimenta a cabeça da cobra de acordo com a velocidade
            snakeX += velocityX;
            snakeY += velocityY;

            // --- REGRAS DE COLISÃO COM AS PAREDES (Game Over se bater) ---
            if (snakeX < 0 || snakeX >= tileCount || snakeY < 0 || snakeY >= tileCount) {
                alert("Fim de jogo! Você bateu na parede.");
                resetGame();
                return;
            }

            // Adiciona a nova posição da cabeça na frente da lista
            snake.unshift({ x: snakeX, y: snakeY });

            // Remove o último elemento da cauda (se a cobra não comeu)
            if (snake.length > tailLength) {
                snake.pop();
            }

            // --- COLISÃO COM A COMIDA ---
            if (snakeX === foodX && snakeY === foodY) {
                tailLength++; // Aumenta o tamanho da cobra
                // Sorteia uma nova posição aleatória para a comida
                foodX = Math.floor(Math.random() * tileCount);
                foodY = Math.floor(Math.random() * tileCount);
            }

            // --- COLISÃO COM O PRÓPRIO CORPO ---
            for (let i = 1; i < snake.length; i++) {
                if (snake[i].x === snakeX && snake[i].y === snakeY) {
                    alert("Fim de jogo! Você bateu em si mesmo.");
                    resetGame();
                }
            }
        }

        function draw() {
            // 1. Limpa a tela pintando de preto
            ctx.fillStyle = "#000";
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 2. Desenha a Comida (Vermelha)
            ctx.fillStyle = "#ff3366";
            ctx.fillRect(foodX * tileSize, foodY * tileSize, tileSize - 2, tileSize - 2);

            // 3. Desenha a Cobra (Verde)
            ctx.fillStyle = "#00ff66";
            for (let i = 0; i < snake.length; i++) {
                ctx.fillRect(snake[i].x * tileSize, snake[i].y * tileSize, tileSize - 2, tileSize - 2);
            }
        }

        // --- CONTROLES DO TECLADO ---
        document.addEventListener('keydown', keyPush);

        function keyPush(event) {
            // Seta para Esquerda (evita andar para trás diretamente)
            if (event.key === "ArrowLeft" && velocityX === 0) {
                velocityX = -1;
                velocityY = 0;
            }
            // Seta para Cima
            else if (event.key === "ArrowUp" && velocityY === 0) {
                velocityX = 0;
                velocityY = -1;
            }
            // Seta para Direita
            else if (event.key === "ArrowRight" && velocityX === 0) {
                velocityX = 1;
                velocityY = 0;
            }
            // Seta para Baixo
            else if (event.key === "ArrowDown" && velocityY === 0) {
                velocityX = 0;
                velocityY = 1;
            }
        }

        function resetGame() {
            snakeX = 10;
            snakeY = 10;
            velocityX = 0;
            velocityY = 0;
            snake = [{ x: 10, y: 10 }];
            tailLength = 2;
        }
    </script>
</body>
</html>
