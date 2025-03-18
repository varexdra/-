# -<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>لعبة شوتر بسيطة</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <script src="game.js"></script>
</body>
</html>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    background: black;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    overflow: hidden;
}

canvas {
    border: 2px solid white;
}
// إعداد اللعبة
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = 800;
canvas.height = 600;

class Player {
    constructor() {
        this.x = canvas.width / 2;
        this.y = canvas.height - 50;
        this.width = 40;
        this.height = 40;
        this.speed = 5;
    }
    
    draw() {
        ctx.fillStyle = "white";
        ctx.fillRect(this.x, this.y, this.width, this.height);
    }

    move(direction) {
        if (direction === "left" && this.x > 0) this.x -= this.speed;
        if (direction === "right" && this.x + this.width < canvas.width) this.x += this.speed;
    }
}

class Bullet {
    constructor(x, y) {
        this.x = x;
        this.y = y;
        this.width = 5;
        this.height = 10;
        this.speed = 7;
    }

    draw() {
        ctx.fillStyle = "red";
        ctx.fillRect(this.x, this.y, this.width, this.height);
    }

    update() {
        this.y -= this.speed;
    }
}

class Enemy {
    constructor(x, y) {
        this.x = x;
        this.y = y;
        this.width = 40;
        this.height = 40;
        this.speed = 2;
    }

    draw() {
        ctx.fillStyle = "green";
        ctx.fillRect(this.x, this.y, this.width, this.height);
    }

    update() {
        this.y += this.speed;
    }
}

const player = new Player();
const bullets = [];
const enemies = [];
let enemySpawnRate = 60; // عدد الفريمات بين ظهور الأعداء
let frameCount = 0;

// حركة اللاعب
document.addEventListener("keydown", (e) => {
    if (e.key === "ArrowLeft") player.move("left");
    if (e.key === "ArrowRight") player.move("right");
    if (e.key === " " || e.key === "ArrowUp") bullets.push(new Bullet(player.x + player.width / 2, player.y));
});

// تحديث اللعبة
function update() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    player.draw();

    bullets.forEach((bullet, index) => {
        bullet.update();
        bullet.draw();
        
        // إزالة الرصاصة إذا خرجت من الشاشة
        if (bullet.y < 0) bullets.splice(index, 1);
    });

    enemies.forEach((enemy, enemyIndex) => {
        enemy.update();
        enemy.draw();

        // إزالة العدو إذا خرج من الشاشة
        if (enemy.y > canvas.height) enemies.splice(enemyIndex, 1);

        // التحقق من التصادم مع الرصاص
        bullets.forEach((bullet, bulletIndex) => {
            if (
                bullet.x < enemy.x + enemy.width &&
                bullet.x + bullet.width > enemy.x &&
                bullet.y < enemy.y + enemy.height &&
                bullet.y + bullet.height > enemy.y
            ) {
                enemies.splice(enemyIndex, 1);
                bullets.splice(bulletIndex, 1);
            }
        });
    });

    // توليد أعداء جدد
    if (frameCount % enemySpawnRate === 0) {
        let enemyX = Math.random() * (canvas.width - 40);
        enemies.push(new Enemy(enemyX, 0));
    }

    frameCount++;
    requestAnimationFrame(update);
}

update();
