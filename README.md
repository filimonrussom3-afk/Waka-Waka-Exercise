 let X = 40;
let y = 40;
let Xspeed = 0;
let yspeed = 0;
let nodesize = 20;
let nodeMargin = 40;
let direction = 0;
let score = 0;
let pellets = [];
let osc;
let canPlay = false;

function setup() {
  createCanvas(windowWidth, windowHeight);
  angleMode(DEGREES);

 
  osc = new p5.Oscillator('sine');
  osc.amp(0);
  osc.start();

  for (let i = nodeMargin + 40; i < width - nodeMargin; i += 60) {
    pellets.push({x: i, y: nodeMargin});
    pellets.push({x: i, y: height - nodeMargin});
  }
  for (let i = nodeMargin + 40; i < height - nodeMargin; i += 60) {
    pellets.push({x: nodeMargin, y: i});
    pellets.push({x: width - nodeMargin, y: i});
  }
}

function draw() {
  background(0);
  drawMap();
  movePacman();
  drawPacman();
  drawPellets();

  fill(255);
  textSize(20);
  textAlign(LEFT);
  text("Score: " + score, 20, 30);
  
  if (!canPlay) {
    text("Click screen once for sound", width/2 - 100, height/2 - 100);
  }
}

function drawMap() {
  fill("blue");
  noStroke();
  circle(nodeMargin, nodeMargin, nodesize);
  circle(width - nodeMargin, nodeMargin, nodesize);
  circle(nodeMargin, height - nodeMargin, nodesize);
  circle(width - nodeMargin, height - nodeMargin, nodesize);
  stroke("orange");
  strokeWeight(2);
  line(nodeMargin, nodeMargin, width - nodeMargin, nodeMargin);
  line(width - nodeMargin, nodeMargin, width - nodeMargin, height - nodeMargin);
  line(width - nodeMargin, height - nodeMargin, nodeMargin, height - nodeMargin);
  line(nodeMargin, height - nodeMargin, nodeMargin, nodeMargin);
}

function movePacman() {
  let nextX = X + Xspeed;
  let nextY = y + yspeed;
  if (nextX >= nodeMargin && nextX <= width - nodeMargin &&
      nextY >= nodeMargin && nextY <= height - nodeMargin) {
    if (Xspeed !== 0 && (y === nodeMargin || y === height - nodeMargin)) X = nextX;
    if (yspeed !== 0 && (X === nodeMargin || X === width - nodeMargin)) y = nextY;
  }
}

function drawPacman() {
  noStroke();
  fill(255, 255, 0);
  let biteSize = 15;
  let mouth = biteSize * sin(frameCount * 8) + biteSize;
  push();
  translate(X, y);
  rotate(direction);
  arc(0, 0, 60, 60, mouth, 360 - mouth, PIE);
  pop();
}

function drawPellets() {
  fill(255);
  noStroke();
  for (let i = pellets.length - 1; i >= 0; i--) {
    circle(pellets[i].x, pellets[i].y, 8);
    if (dist(X, y, pellets[i].x, pellets[i].y) < 25) {
      pellets.splice(i, 1);
      score += 10;
      
      // --- SOUND ADDITION 3 ---
      if (canPlay) {
        osc.freq(700);
        osc.amp(0.2, 0.05);
        osc.amp(0, 0.1, 0.1);
      }
    }
  }
  if (pellets.length === 0) {
    fill("lime");
    textSize(40);
    textAlign(CENTER);
    text("YOU WIN!", width/2, height/2);
    noLoop();
  }
}

function keyPressed() {
  if (key === "a" || keyCode === LEFT_ARROW) { Xspeed = -2; yspeed = 0; direction = 180; }
  if (key === "d" || keyCode === RIGHT_ARROW) { Xspeed = 2; yspeed = 0; direction = 0; }
  if (key === "w" || keyCode === UP_ARROW) { yspeed = -2; Xspeed = 0; direction = 270; }
  if (key === "s" || keyCode === DOWN_ARROW) { yspeed = 2; Xspeed = 0; direction = 90; }
  if (key === "c") { Xspeed = 0; yspeed = 0; }
}
 
function mousePressed() {
  userStartAudio();  
  canPlay = true;
}


