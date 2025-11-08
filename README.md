<!DOCTYPE html>
<html lang="id">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🍇 Gosok Bom</title>
<style>
  body {
    background: linear-gradient(135deg, #000, #1a1a1a);
    color: gold;
    font-family: 'Poppins', sans-serif;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
  }
  h1 {
    color: gold;
    text-shadow: 0 0 10px gold, 0 0 20px #ffb300;
  }
  .grid {
    display: grid;
    grid-template-columns: repeat(4, 100px);
    gap: 12px;
    margin-top: 20px;
  }
  .cell {
    position: relative;
    width: 100px;
    height: 100px;
    background: #222;
    border: 2px solid gold;
    border-radius: 10px;
    overflow: hidden;
  }
  .content {
    font-size: 2em;
    color: gold;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 100%;
  }
  canvas {
    position: absolute;
    top: 0; left: 0;
    width: 100%;
    height: 100%;
    cursor: grab;
    border-radius: 10px;
  }
  .message {
    margin-top: 15px;
    font-size: 1.2em;
    color: gold;
  }
  @keyframes shake {
    0%, 100% {transform: translate(0, 0);}
    25% {transform: translate(-5px, 5px);}
    50% {transform: translate(5px, -5px);}
    75% {transform: translate(-5px, 5px);}
  }
  .boom {
    animation: shake 0.4s ease-in-out 3;
    background: radial-gradient(circle, red, darkred);
  }
</style>
</head>
<body>
  <h1>🍇 Bom Gosok</h1>
  <p>Pilih kotak — gosok sampai terbuka, tapi hati-hati jangan sampai bom!</p>
  <div class="grid" id="grid"></div>
  <div class="message" id="message"></div>

<script>
  const grid = document.getElementById('grid');
  const message = document.getElementById('message');
  const fruits = ["🍎","🍌","🍇","🍒","🍍","🍉","🍊","🥝"];
  const totalCells = 12;
  const bombIndex = Math.floor(Math.random() * totalCells);

  for (let i = 0; i < totalCells; i++) {
    const cell = document.createElement('div');
    cell.classList.add('cell');

    const content = document.createElement('div');
    content.classList.add('content');
    content.textContent = (i === bombIndex) ? "💣" : fruits[Math.floor(Math.random() * fruits.length)];
    cell.appendChild(content);

    // Buat lapisan gosokan (canvas)
    const canvas = document.createElement('canvas');
    canvas.width = 100;
    canvas.height = 100;
    cell.appendChild(canvas);

    grid.appendChild(cell);

    const ctx = canvas.getContext('2d');
    ctx.fillStyle = "#888";
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    let isDrawing = false;
    let scratched = 0;
    const totalArea = canvas.width * canvas.height;

    canvas.addEventListener('mousedown', () => isDrawing = true);
    canvas.addEventListener('mouseup', () => isDrawing = false);
    canvas.addEventListener('mouseleave', () => isDrawing = false);
    canvas.addEventListener('mousemove', (e) => {
      if (!isDrawing) return;
      const rect = canvas.getBoundingClientRect();
      const x = e.clientX - rect.left;
      const y = e.clientY - rect.top;
      ctx.globalCompositeOperation = 'destination-out';
      ctx.beginPath();
      ctx.arc(x, y, 12, 0, Math.PI * 2);
      ctx.fill();

      scratched++;
      if (scratched % 20 === 0) {
        const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
        let transparentPixels = 0;
        for (let j = 3; j < imageData.data.length; j += 4) {
          if (imageData.data[j] === 0) transparentPixels++;
        }
        const percent = transparentPixels / totalArea * 100;
        if (percent > 50) {
          canvas.remove();
          if (i === bombIndex) {
            cell.classList.add('boom');
            message.innerHTML = "💥 BOOM! Kamu KENA BOM!";
            setTimeout(() => alert("Game Over! Coba lagi!"), 500);
          }
        }
      }
    });
  }
</script>
</body>
</html>
