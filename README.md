<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>리듬게임 프로토타입</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body {
      background: radial-gradient(circle at center, #150050, #000);
      overflow: hidden;
      font-family: sans-serif;
    }
    canvas {
      display: block;
      margin: 0 auto;
      background: transparent;
    }
    #score {
      position: absolute;
      top: 20px;
      left: 20px;
      color: #fff;
      font-size: 24px;
      z-index: 2;
    }
  </style>
</head>
<body>
  <div id="score">Score: 0</div>
  <canvas id="gameCanvas" width="480" height="720"></canvas>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const lanes = [80, 160, 240, 320, 400];
    let notes = [];
    let score = 0;

    class Note {
      constructor(x, y, lane) {
        this.x = x;
        this.y = y;
        this.lane = lane;
        this.hit = false;
      }
      update() {
        this.y += 5;
      }
      draw() {
        ctx.fillStyle = '#ff6600';
        ctx.beginPath();
        ctx.arc(this.x, this.y, 20, 0, Math.PI * 2);
        ctx.fill();
      }
    }

    function spawnNote() {
      const laneIndex = Math.floor(Math.random() * lanes.length);
      const x = lanes[laneIndex];
      notes.push(new Note(x, -30, laneIndex));
    }

    function updateGame() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      notes.forEach(note => {
        note.update();
        note.draw();
      });
      notes = notes.filter(note => note.y < canvas.height + 30);
      requestAnimationFrame(updateGame);
    }

    function hitNote(laneIndex) {
      for (let note of notes) {
        if (!note.hit && note.lane === laneIndex && Math.abs(note.y - 680) < 30) {
          note.hit = true;
          score += 100;
          document.getElementById('score').innerText = 'Score: ' + score;
          return;
        }
      }
    }

    // 키보드 입력: A, S, D, F, G
    window.addEventListener('keydown', (e) => {
      const keys = ['a', 's', 'd', 'f', 'g'];
      const index = keys.indexOf(e.key.toLowerCase());
      if (index !== -1) hitNote(index);
    });

    // 기본 노트 생성 간격
    setInterval(spawnNote, 800);
    updateGame();
  </script>
</body>
</html>
