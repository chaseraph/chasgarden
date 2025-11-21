---
{"dg-publish":true,"permalink":"/html-for-snake-game-in-ghost-cms/"}
---

Simply paste the code below into an HTML code block and swap out the three image files and the three phrases under the "START EDITING HERE" header. 

[View a demo online here](https://www.newsinthegrove.com/column-1925-in-forest-grove-play-snake-learn-history/#:~:text=Play%20a%20little%20snake%20game%20below). 

```
<div id="snake-game-wrapper">
    <style>
        /* Scoped Styles */
        #snake-game-wrapper {
            --grid-color: rgba(255, 255, 255, 0.15);
            --snake-color: #00ff88;
            --food-color: #ff0055;
            --text-color: #ffffff;
            --bg-overlay: rgba(0, 0, 0, 0.75);
            --font-family: 'Courier New', Courier, monospace;
            
            /* CHANGED: Switched from flex to block to prevent shrinking */
            display: block;
            width: 100%;
            margin: 40px 0;
            background: transparent;
            font-family: var(--font-family);
        }

        #snake-game-wrapper .game-container {
            /* ADDED: margin auto centers the block element */
            margin: 0 auto;
            
            position: relative;
            width: 100%;
            max-width: 600px;
            aspect-ratio: 1 / 1;
            background-size: cover;
            background-position: center;
            border: 2px solid #333;
            box-shadow: 0 0 20px rgba(0,0,0,0.5);
            overflow: hidden;
            user-select: none;
            background-color: #111;
        }

        #snake-game-wrapper .bg-overlay {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: var(--bg-overlay);
            z-index: 1;
        }

        #snake-game-wrapper canvas {
            position: absolute;
            top: 0;
            left: 0;
            z-index: 2;
            width: 100%;
            height: 100%;
            display: block;
        }

        /* UI Layers */
        #snake-game-wrapper .ui-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 10;
            pointer-events: none;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        #snake-game-wrapper .hud {
            position: absolute;
            top: 20px; /* Moved down slightly */
            left: 50%; /* Center horizontally */
            transform: translateX(-50%); /* Center horizontally */
            width: auto; /* Width adjusts to content */
            max-width: 90%; /* Prevent it from being too wide */
            text-align: center;
            color: var(--text-color);
            z-index: 11;
            text-shadow: 1px 1px 0 #000;
            line-height: 1.2;
            background-color: rgba(0, 0, 0, 0.6); /* Semi-transparent black background */
            padding: 10px 20px; /* Add padding around the text */
            border-radius: 8px; /* Rounded corners */
        }

        #snake-game-wrapper .hud-label {
            font-size: 12px;
            opacity: 0.8;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin: 0;
        }

        #snake-game-wrapper .hud-target {
            font-size: 18px;
            font-weight: bold;
            color: var(--food-color);
            margin: 5px 0 0 0;
        }

        #snake-game-wrapper .progress-container {
            display: flex;
            gap: 5px;
            justify-content: center;
            margin-top: 5px;
        }
        
        #snake-game-wrapper .progress-dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: #444;
            border: 1px solid #666;
            transition: background 0.2s;
        }

        #snake-game-wrapper .progress-dot.active {
            background: var(--snake-color);
            box-shadow: 0 0 5px var(--snake-color);
        }

        #snake-game-wrapper .screen {
            background: rgba(0, 0, 0, 0.85);
            padding: 2rem;
            border-radius: 8px;
            text-align: center;
            color: white;
            border: 1px solid #444;
            pointer-events: auto;
            display: none;
            max-width: 80%;
            box-sizing: border-box;
        }

        #snake-game-wrapper .screen.active {
            display: block;
            animation: fadeIn 0.3s ease;
        }

        #snake-game-wrapper h2.game-title { 
            margin: 0 0 10px 0; 
            font-size: 24px; 
            color: var(--snake-color); 
            line-height: 1.2;
            text-transform: uppercase;
        }

        #snake-game-wrapper p.game-text { 
            font-size: 14px; 
            line-height: 1.4; 
            margin-bottom: 20px; 
            color: #fff;
        }
        
        #snake-game-wrapper button.game-btn {
            background: var(--snake-color);
            color: #000;
            border: none;
            padding: 10px 20px;
            font-family: var(--font-family);
            font-weight: bold;
            cursor: pointer;
            text-transform: uppercase;
            font-size: 14px;
            border-radius: 4px;
            transition: background 0.2s;
        }

        #snake-game-wrapper button.game-btn:hover {
            background: #fff;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: scale(0.9); }
            to { opacity: 1; transform: scale(1); }
        }
    </style>

    <div class="game-container" id="snake-canvas-container">
        <div class="bg-overlay"></div>
        <canvas id="gameCanvas"></canvas>

        <div class="hud">
            <div id="level-label" class="hud-label">READY?</div>
            <!-- CHANGED: Default text so story doesn't spoil before start -->
            <div id="target-word" class="hud-target">PRESS START</div>
            <div class="progress-container" id="dots-container"></div>
        </div>

        <div class="ui-layer">
            
            <!-- Start Screen -->
            <div id="start-screen" class="screen active">
                <h2 class="game-title">STORY SNAKE</h2>
                <p class="game-text">Complete all 3 levels.<br>Use Arrow Keys or Swipe.</p>
                <!-- RESTORED: The Start Game button was missing here -->
                <button class="game-btn" onclick="snakeGame.startGame()">Start Game</button>
            </div>

            <!-- Game Over Screen -->
            <div id="game-over-screen" class="screen">
                <h2 class="game-title" style="color: #ff4444">GAME OVER</h2>
                <p id="fail-reason" class="game-text">You hit a wall!</p>
                <button class="game-btn" onclick="snakeGame.retryLevel()">Try Again</button>
            </div>

            <!-- Win Screen -->
            <div id="win-screen" class="screen">
                <h2 class="game-title">SUCCESS!</h2>
                <p id="win-message" class="game-text">You completed the story.</p>
                <button class="game-btn" onclick="snakeGame.startGame()">Play Again</button>
            </div>
        </div>
    </div>

    <script>
        var snakeGame = (function() {
            
            /* ================= START EDITING HERE ================= */
            const CONFIG = {
                // 1. LEVEL IMAGES
                // Add one image URL for each level below.
                levelImages: [
                    "https://www.newsinthegrove.com/content/images/size/w1000/2025/11/ViewScan_0016-5.jpg", // Level 1
                    "https://www.newsinthegrove.com/content/images/2025/11/ViewScan_0017-4.jpg", // Level 2
                    "https://www.newsinthegrove.com/content/images/2025/11/hist4-4.png"  // Level 3
                ],
                
                // 2. THE PHRASES
                // IMPORTANT: Use Double Quotes "..." around words.
                // IMPORTANT: Add a comma , after each line (except the last one).
                phrases: [
                    "In 1925, Forest Grove moviegoers could see two silent films playing at the Star Theatre, now called Theatre in the Grove",
                    "The Fern Hill community had a school in 1925",
                    "A Banks veteran took home a trophy at the Portland Armistice Day shoot" 
                ],
                
                // 3. GAME SETTINGS
                itemsPerLevel: 3,
                speed: 220
            };
            /* ================= STOP EDITING HERE ================= */

            // Logic below this line does not need editing
            const wrapper = document.getElementById('snake-game-wrapper');
            const canvas = wrapper.querySelector('#gameCanvas');
            const ctx = canvas.getContext('2d');
            const container = wrapper.querySelector('#snake-canvas-container');
            const startScreen = wrapper.querySelector('#start-screen');
            const gameOverScreen = wrapper.querySelector('#game-over-screen');
            const winScreen = wrapper.querySelector('#win-screen');
            const targetWordEl = wrapper.querySelector('#target-word');
            const levelLabelEl = wrapper.querySelector('#level-label');
            const dotsContainer = wrapper.querySelector('#dots-container');
            const failReasonEl = wrapper.querySelector('#fail-reason');
            
            // Helper to set background
            function updateBackground() {
                // Use current level image, or fallback to the first one if missing
                const img = CONFIG.levelImages[levelIndex] || CONFIG.levelImages[0];
                container.style.backgroundImage = `url('${img}')`;
            }

            let snake = [];
            let food = { x: 0, y: 0 };
            let direction = { x: 0, y: 0 };
            let nextDirection = { x: 0, y: 0 };
            let levelIndex = 0;
            let itemsCollectedInLevel = 0;
            let gameInterval;
            let gridSize = 20;
            let tileCount = 20;
            let isGameRunning = false;

            function resizeCanvas() {
                const rect = container.getBoundingClientRect();
                canvas.width = rect.width;
                canvas.height = rect.height;
                tileCount = 20;
                gridSize = canvas.width / tileCount;
                if(!isGameRunning && snake.length > 0) draw();
            }

            function createProgressDots() {
                dotsContainer.innerHTML = '';
                for(let i=0; i<CONFIG.itemsPerLevel; i++) {
                    let dot = document.createElement('div');
                    dot.className = 'progress-dot';
                    dotsContainer.appendChild(dot);
                }
            }

            // CHANGED: Added parameter to control if we reset back to Level 1
            function startGame(resetLevel = true) {
                resizeCanvas();
                createProgressDots();
                snake = [{ x: 10, y: 10 }, { x: 10, y: 11 }, { x: 10, y: 12 }];
                direction = { x: 0, y: -1 };
                nextDirection = { x: 0, y: -1 };
                
                // Only reset to Level 1 if explicitly asked (Start Game / Play Again)
                // Otherwise (Try Again), we stay on the current levelIndex
                if (resetLevel) {
                    levelIndex = 0;
                }
                
                itemsCollectedInLevel = 0;
                isGameRunning = true;

                updateBackground(); // Set initial background
                hideScreens();
                updateHUD();
                spawnFood();

                if (gameInterval) clearInterval(gameInterval);
                gameInterval = setInterval(gameLoop, CONFIG.speed);
                canvas.focus();
            }

            function hideScreens() {
                startScreen.classList.remove('active');
                gameOverScreen.classList.remove('active');
                winScreen.classList.remove('active');
            }

            function spawnFood() {
                let valid = false;
                while (!valid) {
                    // CHANGED: Generate random positions between 1 and tileCount-2
                    // This ensures food never appears on the very edge (0 or tileCount-1)
                    food.x = Math.floor(Math.random() * (tileCount - 2)) + 1;
                    food.y = Math.floor(Math.random() * (tileCount - 2)) + 1;
                    
                    valid = true;
                    for (let segment of snake) {
                        if (segment.x === food.x && segment.y === food.y) {
                            valid = false;
                            break;
                        }
                    }
                }
            }

            function updateHUD() {
                if (levelIndex < CONFIG.phrases.length) {
                    targetWordEl.textContent = CONFIG.phrases[levelIndex];
                    levelLabelEl.textContent = `LEVEL ${levelIndex + 1}`;
                } else {
                    targetWordEl.textContent = "COMPLETE!";
                }
                
                const dots = dotsContainer.querySelectorAll('.progress-dot');
                dots.forEach((dot, idx) => {
                    if (idx < itemsCollectedInLevel) dot.classList.add('active');
                    else dot.classList.remove('active');
                });
            }

            function gameLoop() {
                update();
                draw();
            }

            function update() {
                direction = nextDirection;
                const head = { x: snake[0].x + direction.x, y: snake[0].y + direction.y };

                if (head.x < 0 || head.x >= tileCount || head.y < 0 || head.y >= tileCount) {
                    gameOver("You hit the wall!");
                    return;
                }

                for (let i = 0; i < snake.length; i++) {
                    if (head.x === snake[i].x && head.y === snake[i].y) {
                        gameOver("You bit yourself!");
                        return;
                    }
                }

                snake.unshift(head);

                if (head.x === food.x && head.y === food.y) {
                    itemsCollectedInLevel++;
                    if (itemsCollectedInLevel >= CONFIG.itemsPerLevel) {
                        itemsCollectedInLevel = 0;
                        levelIndex++;
                        
                        if (levelIndex >= CONFIG.phrases.length) {
                            updateHUD();
                            winGame();
                            return;
                        }
                        
                        // Level Up! Change Background
                        updateBackground();
                    }
                    updateHUD();
                    spawnFood();
                } else {
                    snake.pop();
                }
            }

            function draw() {
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                ctx.strokeStyle = getComputedStyle(wrapper).getPropertyValue('--grid-color');
                ctx.lineWidth = 1;
                for (let x = 0; x <= canvas.width; x += gridSize) {
                    ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
                }
                for (let y = 0; y <= canvas.height; y += gridSize) {
                    ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
                }

                ctx.fillStyle = getComputedStyle(wrapper).getPropertyValue('--snake-color');
                snake.forEach((segment) => {
                    const pad = 2; 
                    ctx.fillRect(segment.x * gridSize + pad, segment.y * gridSize + pad, gridSize - (pad*2), gridSize - (pad*2));
                });

                const foodX = food.x * gridSize + gridSize/2;
                const foodY = food.y * gridSize + gridSize/2;
                const radius = gridSize / 3;
                ctx.beginPath();
                ctx.arc(foodX, foodY, radius, 0, 2 * Math.PI);
                ctx.fillStyle = getComputedStyle(wrapper).getPropertyValue('--food-color');
                ctx.fill();
            }

            function gameOver(reason) {
                isGameRunning = false;
                clearInterval(gameInterval);
                failReasonEl.innerText = reason;
                gameOverScreen.classList.add('active');
            }

            function winGame() {
                isGameRunning = false;
                clearInterval(gameInterval);
                winScreen.classList.add('active');
            }

            window.addEventListener('resize', resizeCanvas);
            window.addEventListener('keydown', e => {
                if (!isGameRunning) return;
                const keys = ['ArrowUp', 'ArrowDown', 'ArrowLeft', 'ArrowRight'];
                if (keys.includes(e.key)) {
                    e.preventDefault(); 
                    switch (e.key) {
                        case 'ArrowUp': if (direction.y === 0) nextDirection = { x: 0, y: -1 }; break;
                        case 'ArrowDown': if (direction.y === 0) nextDirection = { x: 0, y: 1 }; break;
                        case 'ArrowLeft': if (direction.x === 0) nextDirection = { x: -1, y: 0 }; break;
                        case 'ArrowRight': if (direction.x === 0) nextDirection = { x: 1, y: 0 }; break;
                    }
                }
            });

            let touchStartX = 0, touchStartY = 0;
            container.addEventListener('touchstart', e => {
                touchStartX = e.changedTouches[0].screenX;
                touchStartY = e.changedTouches[0].screenY;
                if(isGameRunning) e.preventDefault();
            }, {passive: false});

            container.addEventListener('touchend', e => {
                if (!isGameRunning) return;
                let touchEndX = e.changedTouches[0].screenX;
                let touchEndY = e.changedTouches[0].screenY;
                const diffX = touchEndX - touchStartX;
                const diffY = touchEndY - touchStartY;
                if (Math.abs(diffX) > Math.abs(diffY)) {
                    if (diffX > 0 && direction.x === 0) nextDirection = { x: 1, y: 0 };
                    else if (diffX < 0 && direction.x === 0) nextDirection = { x: -1, y: 0 };
                } else {
                    if (diffY > 0 && direction.y === 0) nextDirection = { x: 0, y: 1 };
                    else if (diffY < 0 && direction.y === 0) nextDirection = { x: 0, y: -1 };
                }
            }, {passive: false});

            // Init
            try {
                createProgressDots();
                // CHANGED: Set the initial background, but don't show the story text yet
                updateBackground();
                // updateHUD(); // Removed to hide story text on start screen
                resizeCanvas();
            } catch (err) {
                console.error("Game Init Error:", err);
            }
            
            // CHANGED: Expose both Start (Full Reset) and Retry (Current Level) functions
            return {
                startGame: function() { startGame(true); },
                retryLevel: function() { startGame(false); }
            };
        })();
    </script>
</div>
```