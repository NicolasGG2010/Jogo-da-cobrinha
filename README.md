<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Jogo da Cobrinha</title>
    <style>
        body {
            background-color: #0d1117;
            color: #ffffff;
            font-family: Arial, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            min-height: 100vh;
            margin: 0;
        }

        h1 {
            color: #00ff66;
            margin-bottom: 5px;
            text-shadow: 0 0 10px rgba(0, 255, 102, 0.5);
        }

        p {
            color: #8b949e;
            margin-top: 0;
            margin-bottom: 15px;
        }

        canvas {
            border: 3px solid #00ff66;
            border-radius: 8px;
            background-color: #000000;
            box-shadow: 0 0 20px rgba(0, 255, 102, 0.3);
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

        // Configuração do tamanho da grade e dos blocos
        const tileSize = 20;
        const tileCount = 20;

        // Posição inicial da cabeça da cobra
        let snakeX = 10;
        let snakeY = 10;

        // Velocidade de movimento (direção)
        let velocityX = 0;
        let velocityY = 0;

        // Trava para evitar mudar de direção duas vezes no mesmo frame
        let direcaoMudou = false;

        // Corpo da cobra (lista de blocos)
        let snake = [
            { x: 10, y: 10 }
        ];
        let tailLength = 2; // Tamanho inicial da cauda

        // Posição inicial da comida
        let foodX = 5;
        let foodY = 5;

        // Loop principal do jogo (100 milissegundos)
        let gameInterval = setInterval(gameLoop, 100);

        function gameLoop() {
            update();
            draw();
            direcaoMudou = false; // Libera a troca de direção para o próximo ciclo
        }

        function update() {
            // Movimenta a cabeça da cobra
            snakeX += velocityX;
            snakeY += velocityY;

            // Se a cobra estiver parada (início do jogo), não executa colisões
            if (velocityX === 0 && velocityY === 0) return;

            // --- COLISÃO COM AS PAREDES ---
            if (snakeX < 0 || snakeX >= tileCount || snakeY < 0 || snakeY >= tileCount) {
                alert("Fim de jogo! Você bateu na parede.");
                resetGame();
                return;
            }

            // Adiciona a nova posição da cabeça na frente da lista
            snake.unshift({ x: snakeX, y: snakeY });

            // Remove o último elemento da cauda (se não comeu)
            if (snake.length > tailLength) {
                snake.pop();
            }

            // --- COLISÃO COM A COMIDA ---
            if (snakeX === foodX && snakeY === foodY) {
                tailLength++;
                // Sorteia nova posição para a comida
                foodX = Math.floor(Math.random() * tileCount);
                foodY = Math.floor(Math.random() * tileCount);
            }

            // --- COLISÃO COM O PRÓPRIO CORPO ---
            for (let i = 1; i < snake.length; i++) {
                if (snake[i].x === snakeX && snake[i].y === snakeY) {
                    alert("Fim de jogo! Você bateu em si mesmo.");
                    resetGame();
                    return;
                }
            }
        }

        function draw() {
            // 1. Limpa a tela
            ctx.fillStyle = "#000000";
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // 2. Desenha a Comida
            ctx.fillStyle = "#ff3366";
            ctx.fillRect(foodX * tileSize, foodY * tileSize, tileSize - 2, tileSize - 2);

            // 3. Desenha a Cobra
            ctx.fillStyle = "#00ff66";
            for (let i = 0; i < snake.length; i++) {
                ctx.fillRect(snake[i].x * tileSize, snake[i].y * tileSize, tileSize - 2, tileSize - 2);
            }
        }

        // --- CONTROLES DO TECLADO ---
        document.addEventListener('keydown', keyPush);

        function keyPush(event) {
            if (direcaoMudou) return; // Ignora se já mudou de direção neste ciclo

            // Seta para Esquerda
            if (event.key === "ArrowLeft" && velocityX === 0) {
                velocityX = -1;
                velocityY = 0;
                direcaoMudou = true;
            }
            // Seta para Cima
            else if (event.key === "ArrowUp" && velocityY === 0) {
                velocityX = 0;
                velocityY = -1;
                direcaoMudou = true;
            }
            // Seta para Direita
            else if (event.key === "ArrowRight" && velocityX === 0) {
                velocityX = 1;
                velocityY = 0;
                direcaoMudou = true;
            }
            // Seta para Baixo
            else if (event.key === "ArrowDown" && velocityY === 0) {
                velocityX = 0;
                velocityY = 1;
                direcaoMudou = true;
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
