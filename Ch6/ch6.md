

# Chapter 6: JavaScript Programming for Game


JavaScript has matured into a robust programming language capable of creating high-performance, visually rich games that run in a web browser without additional installations. With WebGL-powered libraries like **Three.js**, developers can render 3D scenes with physics, lighting, animations, and user interaction — all with JavaScript.

In this chapter, you will:

* Learn JavaScript’s essential features for game development.
* Structure your game code using objects, classes, and modules.
* Implement the game loop for real-time updates.
* Create and manipulate 3D objects with Three.js.
* Handle player movement, collision detection, and game events.
* Optimize and deploy your browser-based game.

**Prerequisites:**

* Basic HTML & CSS knowledge.
* Familiarity with coordinate systems.

**Required Tools:**

* **VS Code** or similar text editor
* **Modern Web Browser** (Chrome, Firefox, Edge)
* **Three.js** library (via CDN or npm)



## 6.1 Introduction to JavaScript for Games

JavaScript is a perfect fit for browser-based games because:

1. **Immediate availability** — every modern browser supports it.
2. **Cross-platform compatibility** — runs on desktops, tablets, and phones.
3. **Integration** — easily works with HTML, CSS, audio, and input devices.


### 6.1.1 The Browser Game Loop

Every real-time game is built on a **loop**:

* **Update** → Change game state based on input, AI, physics.
* **Render** → Draw the updated state to the screen.

```javascript
function gameLoop() {
    update(); // Move objects, process collisions
    render(); // Draw the updated scene
    requestAnimationFrame(gameLoop);
}

gameLoop();
```

**Diagram – Game Loop Flow**

```
   Input Events
        ↓
   Update Game State
        ↓
   Render Graphics
        ↓
   Repeat
```


### 6.1.2 HTML Canvas vs WebGL

* **Canvas 2D** — Great for 2D games, sprites, tile maps.
* **WebGL** — GPU-accelerated, enables 3D rendering.
* **Three.js** — Abstraction layer on top of WebGL for simpler development.



**EXERCISE 6.1**

1. Create a blank HTML file and set up a `<canvas>` tag.
2. Write a `gameLoop()` that logs `"Frame update"` every frame.



## 6.2 JavaScript Fundamentals for Game Development


### 6.2.1 Variables and Data Types

```javascript
let playerName = "Hero";
let health = 100;
let isAlive = true;
let position = { x: 0, y: 0 };
```

**Best Practice:** Use `const` for values that do not change, and `let` for values that do.


### 6.2.2 Functions and Reusability

```javascript
function takeDamage(player, amount) {
    player.health -= amount;
    if (player.health <= 0) {
        console.log("Game Over");
    }
}
```

**EXERCISE 6.2**
Write a `healPlayer(player, amount)` function that increases health but caps at `MAX_HEALTH`.


### 6.2.3 Classes and Object-Oriented Design

```javascript
class Enemy {
    constructor(name, health) {
        this.name = name;
        this.health = health;
    }
    takeDamage(amount) {
        this.health -= amount;
        if (this.health <= 0) {
            console.log(`${this.name} defeated!`);
        }
    }
}
```

**Diagram – Player & Enemy Objects**

```
Player Object
 ├─ health
 ├─ position
 └─ score

Enemy Object
 ├─ health
 ├─ position
 └─ AI behavior
```



## 6.3 Structuring Game Code

### 6.3.1 Separation of Concerns

Organize your code into **modules**:

* **player.js** → player controls, stats.
* **enemy.js** → enemy AI, behavior.
* **game.js** → main loop, state transitions.

**Folder Structure Example:**

```
/game
   index.html
   /js
      game.js
      player.js
      enemy.js
      utils.js
```



### 6.3.2 Game States

* `MENU`
* `PLAYING`
* `PAUSED`
* `GAME_OVER`

```javascript
let gameState = "MENU";
```



## 6.4 Introduction to Three.js


### 6.4.1 Setting Up Three.js

```html
<script src="https://cdn.jsdelivr.net/npm/three@0.155.0/build/three.min.js"></script>
```

**Basic Scene Setup:**

```javascript
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(75, window.innerWidth/window.innerHeight, 0.1, 1000);
const renderer = new THREE.WebGLRenderer();
renderer.setSize(window.innerWidth, window.innerHeight);
document.body.appendChild(renderer.domElement);
```



**Illustration – Basic Three.js Scene**
(A screenshot-style diagram showing a cube in 3D space with axes labeled.)



### 6.4.2 Adding Objects

```javascript
const geometry = new THREE.BoxGeometry();
const material = new THREE.MeshBasicMaterial({ color: 0x00ff00 });
const cube = new THREE.Mesh(geometry, material);
scene.add(cube);
```


## 6.5 Player Controls



### 6.5.1 Keyboard Input

```javascript
window.addEventListener('keydown', (event) => {
    if (event.key === 'w') moveForward();
});
```



### 6.5.2 Mouse Look

Use `PointerLockControls` in Three.js for FPS camera movement.



**Diagram – FPS Control Flow**

```
Mouse Move → Camera Rotation
Keyboard WASD → Player Translation
```



## 6.6 Shooting Mechanics



### 6.6.1 Bullet Prefab

```javascript
function createBullet(position, direction) {
    const geometry = new THREE.SphereGeometry(0.1, 8, 8);
    const material = new THREE.MeshBasicMaterial({ color: 0xffff00 });
    const bullet = new THREE.Mesh(geometry, material);
    bullet.position.copy(position);
    bullet.userData.velocity = direction.clone().multiplyScalar(0.5);
    scene.add(bullet);
    bullets.push(bullet);
}
```



**Screenshot – Bullet Prefab in Inspector**
(Realistic Unity-style inspector showing Rigidbody + Collider settings.)



## 6.7 Enemy AI


### 6.7.1 Basic Follow AI

```javascript
function updateEnemy(enemy) {
    const direction = player.position.clone().sub(enemy.position).normalize();
    enemy.position.add(direction.multiplyScalar(0.02));
}
```



📷 **Screenshot – Enemy Prefab with Health Script**
(Unity inspector with a script showing health variables.)



## **6.8 Collision Detection**



### **6.8.1 Bounding Box Intersections**

```javascript
const bulletBox = new THREE.Box3().setFromObject(bullet);
const enemyBox = new THREE.Box3().setFromObject(enemy);

if (bulletBox.intersectsBox(enemyBox)) {
    enemy.takeDamage(10);
}
```



## 6.9 Optimization and Deployment



### 6.9.1 Reducing Draw Calls

* Merge static meshes.
* Use instancing for repeated objects.

### 6.9.2 Deploying to Web

* Host on GitHub Pages, Netlify, or your own server.



## 6.10 Chapter Summary

In this chapter you:

* Learned JavaScript fundamentals for games.
* Set up Three.js for 3D rendering.
* Implemented player movement, shooting, and enemies.
* Understood collision detection and optimization techniques.



**EXERCISES**

1. Add jumping to the player.
2. Make enemies patrol instead of instantly chasing.
3. Add a score counter when enemies are destroyed.


