
# **Chapter 6 – JavaScript Programming for Game**

JavaScript has evolved far beyond being just a “scripting language” for simple webpage interactions. Today, it powers complex browser-based games with full 3D graphics, physics, multiplayer networking, and responsive UI systems.

In this chapter, you will:

* Learn JavaScript fundamentals for interactive games.
* Structure code using functions, classes, and states.
* Create and animate 3D scenes using **Three.js**.
* Implement player movement, shooting, enemy spawning, and collision detection.
* Understand how to optimize for performance in browser-based games.

**Prerequisites:**

* Basic knowledge of HTML & CSS.
* Ability to open a `.html` file in a browser and use VS Code or a similar editor.

**Tools Required:**

* **VS Code** (or any text editor)
* **Web browser** (Chrome, Firefox, or Edge)
* **Three.js** (via CDN link or npm install)

---

## **6.1 Introduction to JavaScript for Games**

JavaScript is the primary language for web game development because:

* It runs **directly in the browser** — no installation required for players.
* It supports **real-time interaction** through the DOM, Canvas, or WebGL.
* Libraries like **Three.js**, **Babylon.js**, and **Phaser** make complex rendering and physics simpler.

---

### **6.1.1 Browser Environment and Game Loops**

A browser game typically runs inside an HTML document, rendering visuals on the **Canvas** or via **WebGL**. The **game loop** is a cycle that continuously updates the game state and redraws the screen.

📄 **Example game loop:**

```javascript
function gameLoop() {
    update(); // Move objects, check collisions
    render(); // Draw the scene
    requestAnimationFrame(gameLoop); // Repeat
}

gameLoop();
```

**Diagram – Browser Game Loop Flow**

```
 ┌───────────────┐
 │ Input Events  │ ←─ Keyboard, mouse, touch
 └───────┬───────┘
         │
 ┌───────▼───────┐
 │ Update State  │ ←─ Player position, enemy movement
 └───────┬───────┘
         │
 ┌───────▼───────┐
 │ Render Frame  │ ←─ Draw to Canvas/WebGL
 └───────┬───────┘
         │
   Repeat Forever
```

---

## **6.2 JavaScript Fundamentals for Game Development**

---

### **6.2.1 Variables and Constants**

Variables store game data like player health, score, and positions.

```javascript
let score = 0;          // Can change during gameplay
const MAX_HEALTH = 100; // Fixed value
```

⚠ **Pitfall:**
Avoid using `var` in modern JavaScript — it has function scope instead of block scope, which can cause unexpected bugs.

**Diagram – Variable Scope in JavaScript**

```
Global Scope ────────────────┐
Function Scope ───┐           │
Block Scope ──────┐│          │
```

---

### **6.2.2 Functions and Modular Code**

Functions allow you to reuse logic — essential in games.

```javascript
function takeDamage(player, amount) {
    player.health -= amount;
    if (player.health <= 0) {
        console.log("Game Over");
    }
}
```

**Try It Yourself:**
Create a `healPlayer` function that increases health but does not exceed `MAX_HEALTH`.

---

### **6.2.3 Control Flow**

Control statements let you branch logic.

```javascript
if (score >= 100) {
    console.log("Level Up!");
} else {
    console.log("Keep going!");
}
```

Game-specific example:

* If player health ≤ 0 → game over screen.
* If all enemies defeated → next level.

---

### **6.2.4 Loops**

Loops are essential for updating multiple game objects.

```javascript
for (let enemy of enemies) {
    enemy.moveTowards(player);
}
```

Diagram – Enemy update cycle:

```
[ Loop Start ]
    ↓
 Update position
    ↓
 Check collision
    ↓
[ Loop End ]
```

---

Alright — I’ll write out **Chapter 6** in full as a 20–30 page instructional textbook section.
It will include:

* Clear section breakdowns (6.0–6.10).
* Explanations, diagrams, and pseudo-screenshots.
* Example code blocks.
* Exercises at the end of major sections.
* Realistic Unity/Three.js-style visuals where relevant.

---