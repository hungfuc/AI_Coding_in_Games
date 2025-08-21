
# Chapter 9: Using Vibe Coding to Develop Three.js JavaScript Games


**Learning Objectives**

By the end of this chapter, you will be able to:

1. Understand the fundamentals of developing 3D games with Three.js in JavaScript.
2. Learn how to use vibe coding — AI-assisted prompt engineering — to generate Three.js game code.
3. Break down game development into stepwise prompts for AI to produce modular, testable code.
4. Follow a complete, real-world example of creating a simple Three.js interactive game using vibe coding.

**Key Concepts**

* Three.js basics: scenes, cameras, meshes, lights, animation loop
* Handling user input and interactions in Three.js
* Modular coding in JavaScript for game objects and logic
* Vibe coding: writing natural language prompts to generate JavaScript game code
* Iterative prompt refinement and integration
* Debugging and testing AI-generated web games

## 9.1 Introduction to Three.js Game Development

Three.js is a powerful JavaScript library for rendering 3D graphics in the browser using WebGL. It provides an accessible way to create interactive 3D experiences and games without the need for native apps.

Key Three.js components:

* **Scene:** Container for all objects.
* **Camera:** Defines the viewpoint.
* **Renderer:** Displays the scene on a canvas.
* **Meshes:** 3D models composed of geometry and materials.
* **Animation loop:** Continuously updates and renders the scene.



## 9.2 Step-by-Step Vibe Coding Strategy for Three.js Games

Vibe coding is about crafting clear, incremental prompts that let an AI generate parts of your Three.js game in manageable chunks.

**Step 1: Define the Game Concept**
Write a simple description of the game you want to build.

**Example:**
> Create a simple Three.js game where the player controls a sphere that can move left and right to catch falling cubes.

**Step 2: Generate Three.js Scene Setup**

**Prompt:**

> Generate JavaScript code using Three.js to create a scene with a perspective camera, basic lighting, and a renderer.

**Step 3: Generate Player Object and Controls**

**Prompt:**

> Generate code to create a controllable sphere mesh that moves left and right based on keyboard input.

**Step 4: Generate Falling Objects Logic**

**Prompt:**

>Generate code to spawn cubes at random X positions at the top of the scene and let them fall down.

**Step 5: Generate Collision Detection**

**Prompt:**

> Generate code to detect when the sphere catches a cube and increase the score.

**Step 6: Generate Score Display**

**Prompt:**

> Generate code to display the current score on the webpage.

**Step 7: Integrate and Refine**

Combine all generated code, test the game, and use follow-up prompts to fix or add features.


## 9.3 Complete Example: Catch the Falling Cubes Game in Three.js


### 9.3.1 Full Game Concept

* Player controls a sphere at the bottom of the screen.
* Cubes fall from the top at random horizontal positions.
* Player moves left and right with arrow keys to catch cubes.
* Each caught cube increases score.
* Simple UI shows the score.



### 9.3.2 Prompt 1: Scene Setup

**Prompt:**

> Generate JavaScript code using Three.js to create a scene with a perspective camera, ambient light, directional light, and WebGLRenderer attached to the document body.

**AI Output:**

```javascript
import * as THREE from 'three';

const scene = new THREE.Scene();

const camera = new THREE.PerspectiveCamera(
  75, window.innerWidth / window.innerHeight, 0.1, 1000
);
camera.position.z = 10;

const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);

const ambientLight = new THREE.AmbientLight(0xffffff, 0.5);
scene.add(ambientLight);

const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
directionalLight.position.set(0, 10, 10);
scene.add(directionalLight);

function animate() {
  requestAnimationFrame(animate);
  renderer.render(scene, camera);
}
animate();
```


### 9.3.3 Prompt 2: Player Sphere and Controls

**Prompt:**

> Add a sphere mesh that can move left and right based on arrow key input in the Three.js scene.

**AI Output:**

```javascript
const playerGeometry = new THREE.SphereGeometry(1, 32, 32);
const playerMaterial = new THREE.MeshStandardMaterial({ color: 0x0077ff });
const player = new THREE.Mesh(playerGeometry, playerMaterial);
scene.add(player);

let moveLeft = false;
let moveRight = false;

window.addEventListener('keydown', (event) => {
  if (event.key === 'ArrowLeft') moveLeft = true;
  if (event.key === 'ArrowRight') moveRight = true;
});
window.addEventListener('keyup', (event) => {
  if (event.key === 'ArrowLeft') moveLeft = false;
  if (event.key === 'ArrowRight') moveRight = false;
});

function updatePlayer() {
  if (moveLeft) player.position.x -= 0.1;
  if (moveRight) player.position.x += 0.1;

  // Clamp position within bounds
  player.position.x = THREE.MathUtils.clamp(player.position.x, -5, 5);
}
```

Modify the `animate` function to call `updatePlayer()`:

```javascript
function animate() {
  requestAnimationFrame(animate);
  updatePlayer();
  renderer.render(scene, camera);
}
```


### 9.3.4 Prompt 3: Falling Cubes

**Prompt:**

> Generate code to spawn cubes at random X positions at the top, falling down each frame, and remove them when they fall below the scene.

**AI Output:**

```javascript
const fallingCubes = [];

function spawnCube() {
  const geometry = new THREE.BoxGeometry(1, 1, 1);
  const material = new THREE.MeshStandardMaterial({ color: 0xff0000 });
  const cube = new THREE.Mesh(geometry, material);

  cube.position.x = (Math.random() - 0.5) * 10; // Range -5 to 5
  cube.position.y = 10;
  scene.add(cube);
  fallingCubes.push(cube);
}

// Spawn a cube every 1 second
setInterval(spawnCube, 1000);

function updateCubes() {
  for (let i = fallingCubes.length - 1; i >= 0; i--) {
    const cube = fallingCubes[i];
    cube.position.y -= 0.05;

    if (cube.position.y < -5) {
      scene.remove(cube);
      fallingCubes.splice(i, 1);
    }
  }
}
```

Update the animation loop:

```javascript
function animate() {
  requestAnimationFrame(animate);
  updatePlayer();
  updateCubes();
  renderer.render(scene, camera);
}
```



### 9.3.5 Prompt 4: Collision Detection and Scoring

**Prompt:**

> "Generate code to detect when the player sphere intersects with a cube, remove the cube, and increment the score."

**AI Output:**

```javascript
let score = 0;

// Simple bounding sphere collision detection
function checkCollisions() {
  for (let i = fallingCubes.length - 1; i >= 0; i--) {
    const cube = fallingCubes[i];
    const distance = player.position.distanceTo(cube.position);
    if (distance < 1.5) { // approximate collision radius
      scene.remove(cube);
      fallingCubes.splice(i, 1);
      score++;
      updateScoreDisplay();
    }
  }
}

function updateScoreDisplay() {
  document.getElementById('score').textContent = `Score: ${score}`;
}
```

Call `checkCollisions()` in the animation loop:

```javascript
function animate() {
  requestAnimationFrame(animate);
  updatePlayer();
  updateCubes();
  checkCollisions();
  renderer.render(scene, camera);
}
```


### 9.3.6 Prompt 5: Score UI Display

**Prompt:**

> Generate HTML and CSS to show the current score at the top-left corner of the webpage.

**AI Output:**

```html
<style>
  #score {
    position: absolute;
    top: 10px;
    left: 10px;
    color: white;
    font-family: Arial, sans-serif;
    font-size: 24px;
  }
</style>
<div id="score">Score: 0</div>
```



### 9.3.7 Integration Summary

By combining all the generated snippets, you get a working Three.js game where the player controls a sphere catching falling cubes and tracking the score.



## 9.4 Summary

* Vibe coding accelerates Three.js game dev by generating modular code from clear natural language prompts.
* Breaking down features into focused prompts yields manageable, testable code segments.
* Iterative refinement and integration help polish the game and add features.
* This method works for prototyping and educational purposes to jumpstart JavaScript 3D game projects.



## Review Questions

1. What are the key Three.js components for building a 3D scene?
2. How does vibe coding improve productivity in JavaScript game development?
3. Describe how you would structure prompts for building an interactive game step-by-step.
4. What are common challenges when integrating AI-generated code?
5. How can collision detection be implemented efficiently in Three.js?



## Exercises

1. Extend the falling cubes game by adding increasing difficulty (e.g., faster falling cubes).
2. Use vibe coding to add sound effects on catching cubes.
3. Create a new prompt sequence for a Three.js maze navigation game and generate initial code.


