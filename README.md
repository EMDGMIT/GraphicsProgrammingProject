# GraphicsProgrammingProject
** by Éanna MacDonough **
This is the README for my Bullet Time, which includes details about the game including instructions detailing how to play the game. Due to issues wit uploading through git, all the project code will be entered below.

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Bullet Time</title>

    <style type="text/css">
      canvas {
        border: 1px solid black;
      }
    </style>

  </head>

  <body>
      <canvas id="gamecanvas" width="700" height="650"></canvas>
 
      <script type="text/javascript">
          
          var circs = [];
          var showScreen = 0;
          
          // Gets a handle to the element with id canvas
          var canvas = document.getElementById("gamecanvas");
          // Get a 2D context for the canvas.
          var ctx = canvas.getContext("2d");
          
          // How often the balls spawn
          var spawnRate = 500;
          // How fast the balls fall
          var spawnRateOfDescent = 5;
          var lastSpawn = -1;
          // save the starting time (used to calculate elapsed time)
          var time = Date.now();
          // variable for players score
          var score = 0;
          
          var player = {
              position: {x: canvas.width / 2, y: 450}, 
			  height: 50, 
			  width: 50
          }
                
          // Starts the animation
          startScreen();
          

          function drawPlayer(){
              ctx.fillStyle = "rgb(0, 255, 0)";
              ctx.beginPath(); 
              ctx.rect(player.position.x, player.position.y, player.width, player.height);
              ctx.fill();
              ctx.closePath();
          }
                  
          // The animation frame
          function step(t) {
              var time = Date.now(); // Get current time
              showScreen = 1;
              // used for spawning purposes early
              if (time > (lastSpawn + spawnRate)) {
                  lastSpawn = time;
                  circ();
              }
              
              ctx.clearRect(0, 0, canvas.width, canvas.height);
              
              // writes score to top of screen
              ctx.font="20px Arial";
              ctx.fillText("Score: " + score, canvas.offsetWidth/2.5, 25);

              //draws each circle
              for (var i = 0; i < circs.length; i++) {
                  var object = circs[i];
                  //If circle reaches bottom, remove from array. Add to score if grey, slow down occurrence and speed if blue
                  if(object.y > canvas.height+10){
                      circs.splice(i,1);
                      if (object.type =="grey")
                        score++
                      if (score >= 10 && score%5 == 0){ // increase ball speed if score is 10 or greater and divisible by five
                          spawnRateOfDescent+=0.15;
                          if(spawnRate > 450)
                            spawnRate -= 150;
                      }
                  }
                  if(collisionCheck(object)){// check if circle has touched square
                      if(object.type == "grey"){ //if grey end game
                          i =circs.length+100; // if true end for loop
                          gameover();
                          break;
                      }
                      else{ //if blue, reduce spawn rate and slow down balls
                          circs.splice(i,1);
                          score++
                          spawnRate+= 300
                          spawnRateOfDescent-=0.45;
                      }
                  }
                  object.y += spawnRateOfDescent; // move circle
                  ctx.beginPath();
                  ctx.arc(object.x, object.y, object.r, 0, Math.PI * 2);
                  ctx.closePath();
                  ctx.fillStyle = object.type;
                  ctx.fill();
              }

              if(collisionCheck(object) == false){ // check if circle has touched square
                  drawPlayer();
                  requestAnimationFrame(step); // if false keep going, else end game
              }
          }
                    
          // Player Movement          
          window.addEventListener("keydown", function(event) { 
        
              if (event.keyCode == 37 && player.position.x > 0){
                  player.position.x -= 5;
              }
              else if (event.keyCode == 39 && player.position.x < canvas.width-player.width){
                  player.position.x += 5;
              }
              
              else if (event.keyCode == 32 && showScreen == 0){
                  step();
              }
              
             if(event.keyCode == 32 && showScreen == 2)
                  startScreen();
               
          });

          
          // Constructor to randomise between grey and blue balls
          function circ(){
              var t;
              // blue balls won't spawn until score is at least 20
              if (Math.random() < 0.10 && score >= 20) {
                  t = "blue";
              } 
              else {
                  t = "grey";
              }
              var Circle = {
                  // set this objects type
                  type: t,
                  // sets x randomly
                  x: Math.floor(Math.random() * (canvas.width - 30) + 15),
                  // set y
                  y: -50,
                  // set r
                  r: 16
              }
              
              // adds to array
              circs.push(Circle);
          }
          
          function collisionCheck(i){ // checks if a circle is inside square
				return (i.x - 8 < player.position.x + player.width && i.x + 8 > player.position.x && i.y < player.position.y + player.height && i.y + 8 > player.position.y)
            
          }

          // game over screen
          function gameover(){ 
              showScreen = 2;
              ctx.clearRect(0, 0, canvas.width, canvas.height);
              ctx.font = "50px Arial";
              ctx.fillStyle ="red";
              ctx.fillText("You got shot! Game Over", 60, canvas.height / 2 - 60);
              ctx.fillText("Your Score: " + score, 175, canvas.height / 2);
              ctx.fillText("Press Space to go back", 75, canvas.height / 2 + 60)
              ctx.fillText("to the Start Screen", 135, canvas.height / 2 + 120)
          }
          
          // game start screen
          function startScreen(){
              //reset Everything
              showScreen = 0;
              spawnRate = 500;
              spawnRateOfDescent = 5;
              lastSpawn = -1;
              time = Date.now();
              score = 0;
              circs.splice(0, circs.length); // empty circle array
              ctx.clearRect(0, 0, canvas.width, canvas.height);
              
              //Start Screen text
              ctx.font = "75px Arial";
              ctx.fillStyle = "green"
              ctx.fillText("Bullet Time", 150, 75);
              
              ctx.font = "50px Arial";
              ctx.fillText("This is you:", 150, 185);
              ctx.fillText("Dodge these bullets:", 50, 245);
              ctx.fillText("Get the blue pill :" , 75 , 305);
			        ctx.fillText("to slow down the bullets" , 40 , 350);
              ctx.fillText("Use the arrow keys to" , 100 , 410);
              ctx.fillText("move left and right" , 130 , 455);
              ctx.fillText("Press Space to start" , 110 , 510);
              
              
              // Displays Player on Start Screen
              ctx.beginPath(); 
              ctx.rect(425, 145, player.width, player.height); 
              ctx.fill();
              ctx.closePath();
			  
			        // Displays Bullet on Start Screen
              ctx.beginPath();
              ctx.arc(540, 230, 16, 0, Math.PI * 2);
              ctx.closePath();
              ctx.fillStyle = "grey";
              ctx.fill();
			  
			  // Displays Blue Pill on Start Screen
              ctx.beginPath();
              ctx.arc(480, 290, 16, 0, Math.PI * 2);
              ctx.closePath();
              ctx.fillStyle = "blue";
              ctx.fill();       
          }

    </script>
  </body>
</html>
