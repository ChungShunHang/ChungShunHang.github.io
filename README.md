# ChungShunHang.github.io
ELEC3120 Homework 1 Website

Name: CHUNG Shun Hang
SID: 20985488

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Cool Pattern Generator</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            color: #fff;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 20px;
        }
        
        header {
            text-align: center;
            margin-bottom: 30px;
            max-width: 800px;
        }
        
        h1 {
            font-size: 2.8rem;
            margin-bottom: 10px;
            background: linear-gradient(to right, #ff7e5f, #feb47b);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-shadow: 0 0 10px rgba(255, 126, 95, 0.3);
        }
        
        .subtitle {
            font-size: 1.2rem;
            opacity: 0.8;
            margin-bottom: 20px;
        }
        
        .container {
            display: flex;
            flex-wrap: wrap;
            gap: 30px;
            justify-content: center;
            max-width: 1200px;
        }
        
        .pattern-container {
            width: 500px;
            height: 500px;
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.4);
            position: relative;
        }
        
        canvas {
            width: 100%;
            height: 100%;
            display: block;
        }
        
        .controls {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 10px;
            padding: 20px;
            width: 300px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
        }
        
        .control-group {
            margin-bottom: 20px;
        }
        
        h2 {
            font-size: 1.5rem;
            margin-bottom: 15px;
            color: #feb47b;
        }
        
        label {
            display: block;
            margin-bottom: 8px;
            font-weight: 500;
        }
        
        input[type="range"] {
            width: 100%;
            margin-bottom: 15px;
        }
        
        .value-display {
            display: inline-block;
            width: 40px;
            text-align: right;
        }
        
        button {
            background: linear-gradient(to right, #ff7e5f, #feb47b);
            border: none;
            color: white;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: transform 0.2s, box-shadow 0.2s;
            width: 100%;
            margin-top: 10px;
        }
        
        button:hover {
            transform: translateY(-2px);
            box-shadow: 0 5px 15px rgba(255, 126, 95, 0.4);
        }
        
        .pattern-buttons {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
            margin-top: 15px;
        }
        
        .pattern-buttons button {
            margin-top: 0;
        }
        
        footer {
            margin-top: 30px;
            text-align: center;
            opacity: 0.7;
            font-size: 0.9rem;
        }
        
        @media (max-width: 900px) {
            .container {
                flex-direction: column;
                align-items: center;
            }
            
            .pattern-container, .controls {
                width: 100%;
                max-width: 500px;
            }
        }
    </style>
</head>
<body>
    <header>
        <h1>Cool Pattern Generator</h1>
        <p class="subtitle">Create mesmerizing geometric patterns with interactive controls</p>
    </header>
    
    <div class="container">
        <div class="pattern-container">
            <canvas id="patternCanvas"></canvas>
        </div>
        
        <div class="controls">
            <div class="control-group">
                <h2>Pattern Controls</h2>
                
                <label for="patternType">Pattern Type:</label>
                <select id="patternType">
                    <option value="circles">Concentric Circles</option>
                    <option value="triangles">Rotating Triangles</option>
                    <option value="hexagons">Hexagonal Grid</option>
                    <option value="waves">Sine Waves</option>
                    <option value="spiral">Spiral</option>
                </select>
                
                <label for="speed">Animation Speed: <span id="speedValue" class="value-display">5</span></label>
                <input type="range" id="speed" min="0" max="10" value="5">
                
                <label for="size">Element Size: <span id="sizeValue" class="value-display">50</span></label>
                <input type="range" id="size" min="10" max="100" value="50">
                
                <label for="density">Pattern Density: <span id="densityValue" class="value-display">5</span></label>
                <input type="range" id="density" min="1" max="10" value="5">
                
                <label for="colorScheme">Color Scheme:</label>
                <select id="colorScheme">
                    <option value="rainbow">Rainbow</option>
                    <option value="ocean">Ocean Blues</option>
                    <option value="sunset">Sunset</option>
                    <option value="forest">Forest Greens</option>
                    <option value="monochrome">Monochrome</option>
                </select>
            </div>
            
            <div class="pattern-buttons">
                <button id="randomize">Randomize</button>
                <button id="pausePlay">Pause</button>
            </div>
            
            <button id="download">Download Pattern</button>
        </div>
    </div>
    
    <footer>
        <p>Refresh the page for a new random pattern | Created with HTML, CSS, and JavaScript</p>
    </footer>

    <script>
        // Get canvas and context
        const canvas = document.getElementById('patternCanvas');
        const ctx = canvas.getContext('2d');
        
        // Set canvas dimensions
        function resizeCanvas() {
            canvas.width = canvas.offsetWidth;
            canvas.height = canvas.offsetHeight;
        }
        
        // Initialize variables
        let animationId;
        let isPlaying = true;
        let time = 0;
        
        // Control elements
        const patternType = document.getElementById('patternType');
        const speedSlider = document.getElementById('speed');
        const speedValue = document.getElementById('speedValue');
        const sizeSlider = document.getElementById('size');
        const sizeValue = document.getElementById('sizeValue');
        const densitySlider = document.getElementById('density');
        const densityValue = document.getElementById('densityValue');
        const colorScheme = document.getElementById('colorScheme');
        const randomizeBtn = document.getElementById('randomize');
        const pausePlayBtn = document.getElementById('pausePlay');
        const downloadBtn = document.getElementById('download');
        
        // Update value displays
        speedSlider.addEventListener('input', () => {
            speedValue.textContent = speedSlider.value;
        });
        
        sizeSlider.addEventListener('input', () => {
            sizeValue.textContent = sizeSlider.value;
        });
        
        densitySlider.addEventListener('input', () => {
            densityValue.textContent = densitySlider.value;
        });
        
        // Randomize button
        randomizeBtn.addEventListener('click', () => {
            patternType.selectedIndex = Math.floor(Math.random() * patternType.options.length);
            speedSlider.value = Math.floor(Math.random() * 11);
            speedValue.textContent = speedSlider.value;
            sizeSlider.value = Math.floor(Math.random() * 91) + 10;
            sizeValue.textContent = sizeSlider.value;
            densitySlider.value = Math.floor(Math.random() * 10) + 1;
            densityValue.textContent = densitySlider.value;
            colorScheme.selectedIndex = Math.floor(Math.random() * colorScheme.options.length);
        });
        
        // Pause/Play button
        pausePlayBtn.addEventListener('click', () => {
            isPlaying = !isPlaying;
            pausePlayBtn.textContent = isPlaying ? 'Pause' : 'Play';
            
            if (isPlaying) {
                animate();
            } else {
                cancelAnimationFrame(animationId);
            }
        });
        
        // Download button
        downloadBtn.addEventListener('click', () => {
            const link = document.createElement('a');
            link.download = 'cool-pattern.png';
            link.href = canvas.toDataURL('image/png');
            link.click();
        });
        
        // Color schemes
        const colorSchemes = {
            rainbow: (t, i, max) => {
                const hue = (t * 0.5 + i * 10) % 360;
                return `hsl(${hue}, 70%, 60%)`;
            },
            ocean: (t, i, max) => {
                const hue = 200 + Math.sin(t * 0.01 + i * 0.1) * 20;
                const saturation = 70 + Math.sin(t * 0.02 + i * 0.2) * 20;
                return `hsl(${hue}, ${saturation}%, 50%)`;
            },
            sunset: (t, i, max) => {
                const hue = 30 + Math.sin(t * 0.01 + i * 0.05) * 10;
                return `hsl(${hue}, 80%, 60%)`;
            },
            forest: (t, i, max) => {
                const hue = 120 + Math.sin(t * 0.01 + i * 0.1) * 20;
                const lightness = 40 + Math.sin(t * 0.02 + i * 0.2) * 10;
                return `hsl(${hue}, 70%, ${lightness}%)`;
            },
            monochrome: (t, i, max) => {
                const brightness = 50 + Math.sin(t * 0.02 + i * 0.1) * 30;
                return `hsl(0, 0%, ${brightness}%)`;
            }
        };
        
        // Pattern drawing functions
        const patterns = {
            circles: (ctx, width, height, t, size, density, getColor) => {
                ctx.clearRect(0, 0, width, height);
                
                const centerX = width / 2;
                const centerY = height / 2;
                const maxRadius = Math.min(width, height) / 2;
                const circleCount = Math.floor(density * 5);
                
                for (let i = 0; i < circleCount; i++) {
                    const radius = (i / circleCount) * maxRadius;
                    const pulse = Math.sin(t * 0.02 + i * 0.5) * 10;
                    const currentRadius = radius + pulse;
                    
                    ctx.beginPath();
                    ctx.arc(centerX, centerY, currentRadius, 0, Math.PI * 2);
                    ctx.strokeStyle = getColor(t, i, circleCount);
                    ctx.lineWidth = 2 + Math.sin(t * 0.03 + i) * 1.5;
                    ctx.stroke();
                }
            },
            
            triangles: (ctx, width, height, t, size, density, getColor) => {
                ctx.clearRect(0, 0, width, height);
                
                const cols = Math.floor(width / size) + 2;
                const rows = Math.floor(height / size) + 2;
                const triangleSize = size * 0.8;
                
                for (let x = 0; x < cols; x++) {
                    for (let y = 0; y < rows; y++) {
                        const centerX = x * size - size/2;
                        const centerY = y * size - size/2;
                        
                        const rotation = t * 0.005 + (x + y) * 0.2;
                        
                        ctx.save();
                        ctx.translate(centerX, centerY);
                        ctx.rotate(rotation);
                        
                        ctx.beginPath();
                        ctx.moveTo(0, -triangleSize/2);
                        ctx.lineTo(triangleSize/2, triangleSize/2);
                        ctx.lineTo(-triangleSize/2, triangleSize/2);
                        ctx.closePath();
                        
                        ctx.fillStyle = getColor(t, x + y * cols, cols * rows);
                        ctx.fill();
                        
                        ctx.restore();
                    }
                }
            },
            
            hexagons: (ctx, width, height, t, size, density, getColor) => {
                ctx.clearRect(0, 0, width, height);
                
                const hexSize = size * 0.8;
                const hexHeight = hexSize * Math.sqrt(3);
                const cols = Math.floor(width / (hexSize * 1.5)) + 2;
                const rows = Math.floor(height / hexHeight) + 2;
                
                for (let x = 0; x < cols; x++) {
                    for (let y = 0; y < rows; y++) {
                        const hexX = x * hexSize * 1.5;
                        const hexY = y * hexHeight;
                        
                        // Offset every other column
                        if (x % 2 === 1) {
                            hexY += hexHeight / 2;
                        }
                        
                        const pulse = Math.sin(t * 0.02 + x * 0.3 + y * 0.2) * 5;
                        const currentSize = hexSize + pulse;
                        
                        drawHexagon(ctx, hexX, hexY, currentSize, getColor(t, x + y * cols, cols * rows));
                    }
                }
            },
            
            waves: (ctx, width, height, t, size, density, getColor) => {
                ctx.clearRect(0, 0, width, height);
                
                const waveCount = density * 2;
                const amplitude = size / 2;
                
                for (let i = 0; i < waveCount; i++) {
                    ctx.beginPath();
                    
                    for (let x = 0; x < width; x += 5) {
                        const y = height/2 + 
                                 Math.sin(x * 0.02 + t * 0.01 + i * 0.5) * amplitude +
                                 Math.sin(x * 0.01 + t * 0.02) * amplitude * 0.5;
                        
                        if (x === 0) {
                            ctx.moveTo(x, y);
                        } else {
                            ctx.lineTo(x, y);
                        }
                    }
                    
                    ctx.strokeStyle = getColor(t, i, waveCount);
                    ctx.lineWidth = 2 + Math.sin(t * 0.03 + i) * 1.5;
                    ctx.stroke();
                }
            },
            
            spiral: (ctx, width, height, t, size, density, getColor) => {
                ctx.clearRect(0, 0, width, height);
                
                const centerX = width / 2;
                const centerY = height / 2;
                const maxRadius = Math.min(width, height) / 2;
                const segments = density * 50;
                
                ctx.beginPath();
                
                for (let i = 0; i <= segments; i++) {
                    const angle = i * 0.1 + t * 0.005;
                    const radius = (i / segments) * maxRadius;
                    
                    const x = centerX + Math.cos(angle) * radius;
                    const y = centerY + Math.sin(angle) * radius;
                    
                    if (i === 0) {
                        ctx.moveTo(x, y);
                    } else {
                        ctx.lineTo(x, y);
                    }
                }
                
                ctx.strokeStyle = getColor(t, 0, 1);
                ctx.lineWidth = 3;
                ctx.stroke();
                
                // Add dots along the spiral
                const dotCount = density * 10;
                
                for (let i = 0; i < dotCount; i++) {
                    const angle = i * 0.5 + t * 0.01;
                    const radius = (i / dotCount) * maxRadius;
                    
                    const x = centerX + Math.cos(angle) * radius;
                    const y = centerY + Math.sin(angle) * radius;
                    
                    const dotSize = 3 + Math.sin(t * 0.02 + i * 0.3) * 2;
                    
                    ctx.beginPath();
                    ctx.arc(x, y, dotSize, 0, Math.PI * 2);
                    ctx.fillStyle = getColor(t, i, dotCount);
                    ctx.fill();
                }
            }
        };
        
        // Helper function to draw a hexagon
        function drawHexagon(ctx, x, y, size, color) {
            ctx.beginPath();
            for (let i = 0; i < 6; i++) {
                const angle = (i * Math.PI) / 3;
                const hexX = x + size * Math.cos(angle);
                const hexY = y + size * Math.sin(angle);
                
                if (i === 0) {
                    ctx.moveTo(hexX, hexY);
                } else {
                    ctx.lineTo(hexX, hexY);
                }
            }
            ctx.closePath();
            ctx.fillStyle = color;
            ctx.fill();
        }
        
        // Animation loop
        function animate() {
            if (!isPlaying) return;
            
            const width = canvas.width;
            const height = canvas.height;
            const speed = parseFloat(speedSlider.value) / 5;
            const size = parseFloat(sizeSlider.value);
            const density = parseFloat(densitySlider.value);
            const selectedPattern = patternType.value;
            const selectedColorScheme = colorScheme.value;
            
            time += speed;
            
            // Get the appropriate color function
            const getColor = colorSchemes[selectedColorScheme];
            
            // Draw the selected pattern
            if (patterns[selectedPattern]) {
                patterns[selectedPattern](ctx, width, height, time, size, density, getColor);
            }
            
            animationId = requestAnimationFrame(animate);
        }
        
        // Initialize and start animation
        window.addEventListener('load', () => {
            resizeCanvas();
            animate();
        });
        
        window.addEventListener('resize', () => {
            resizeCanvas();
        });
        
        // Randomize on first load
        randomizeBtn.click();
    </script>
</body>
</html>
