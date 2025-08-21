

# **8.1 Networking in Unity: Coding Skills & Examples**

---

## Introduction

Networking is the backbone of multiplayer experiences, allowing multiple devices to communicate and share data in real time. In XR games, this communication enables players to interact, see each other’s movements, and collaborate within a shared virtual space.

This section covers:

* Fundamental networking concepts relevant to XR games
* The role of transport protocols like TCP and UDP
* How Unity supports networking through its Transport Layer and Netcode framework
* Practical code examples demonstrating basic network connections and data transfer

By the end of this section, you will understand the key building blocks of networking in Unity and be ready to implement simple client-server communication.

---

## 8.1.1 Fundamentals of Networking in XR Games

### Client-Server vs. Peer-to-Peer

**Client-Server Model**:
In this common model, one device acts as the *server* hosting the game state, while others are *clients* connecting to it. The server manages authoritative game logic and broadcasts updates to clients.

* **Advantages:** Easier to control game state and prevent cheating.
* **Disadvantages:** Requires a powerful server, potential latency for distant clients.

**Peer-to-Peer Model**:
All players connect directly to each other without a centralized server. Each peer shares its state with others.

* **Advantages:** Less server cost, more direct communication.
* **Disadvantages:** Complex to synchronize, more vulnerable to cheating, challenging in XR due to latency sensitivity.

---

### Networking Data Types: Reliable vs. Unreliable

Networking protocols typically transmit data either:

* **Reliably (TCP)**: Guarantees delivery, preserves order but higher latency. Good for chat messages, game state updates that must arrive.
* **Unreliably (UDP)**: No guarantee of delivery or order but lower latency. Preferred for fast-paced data like player movement in XR.

---

## 8.1.2 Transport Protocols: TCP and UDP

### TCP (Transmission Control Protocol)

* Connection-oriented protocol
* Ensures all packets arrive and are in order
* Handles retransmissions
* Higher latency due to acknowledgments and error correction

### UDP (User Datagram Protocol)

* Connectionless protocol
* Sends packets without guaranteeing delivery
* Lower latency, no retransmission overhead
* Applications must handle lost or out-of-order packets if necessary

---

### When to Use Which in XR Games?

| Data Type                                   | Protocol                     | Reason                                        |
| ------------------------------------------- | ---------------------------- | --------------------------------------------- |
| Player position, head & controller movement | UDP                          | Fast updates, tolerate occasional packet loss |
| Game state changes (score, inventory)       | TCP                          | Requires guaranteed delivery                  |
| Voice chat                                  | UDP (via protocols like RTP) | Low latency required                          |

---

## 8.1.3 Networking in Unity: Transport Layer Overview

Unity provides low-level networking support via the **Unity Transport Package**, which abstracts the details of TCP/UDP socket handling. On top of that, Unity’s **Netcode for GameObjects** offers a high-level multiplayer framework that simplifies state synchronization, RPCs, and object ownership.

---

## 8.1.4 Example 1 — Simple TCP Client in Unity

This example demonstrates how to create a basic TCP client in Unity that connects to a server, sends a message, and handles connection errors.

```csharp
using System;
using System.Net.Sockets;
using System.Text;
using UnityEngine;

public class SimpleTCPClient : MonoBehaviour
{
    private TcpClient client;
    private NetworkStream stream;

    void Start()
    {
        try
        {
            // Connect to server at localhost (127.0.0.1), port 5000
            client = new TcpClient("127.0.0.1", 5000);
            stream = client.GetStream();
            Debug.Log("Connected to server!");

            // Prepare message
            string message = "Hello from Unity!";
            byte[] data = Encoding.ASCII.GetBytes(message);

            // Send message
            stream.Write(data, 0, data.Length);
            Debug.Log("Message sent to server.");
        }
        catch (Exception e)
        {
            Debug.LogError("Connection error: " + e.Message);
        }
    }

    void OnApplicationQuit()
    {
        // Clean up connections
        stream?.Close();
        client?.Close();
    }
}
```

### Explanation

* The `TcpClient` class handles the connection to the TCP server.
* The `NetworkStream` lets you send and receive data over the connection.
* The example sends a simple ASCII message upon connecting.
* Error handling ensures the app won’t crash on connection failure.

---

## 8.1.5 Example 2 — Simple UDP Client in Unity

UDP communication requires more manual management but offers lower latency. Below is a basic UDP client that sends a message to a UDP server.

```csharp
using System.Net;
using System.Net.Sockets;
using System.Text;
using UnityEngine;

public class SimpleUDPClient : MonoBehaviour
{
    private UdpClient udpClient;
    private IPEndPoint serverEndPoint;

    void Start()
    {
        udpClient = new UdpClient();
        serverEndPoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 5001);

        string message = "Hello via UDP!";
        byte[] data = Encoding.UTF8.GetBytes(message);

        udpClient.Send(data, data.Length, serverEndPoint);
        Debug.Log("UDP message sent.");
    }

    void OnApplicationQuit()
    {
        udpClient.Close();
    }
}
```

### Key Notes:

* UDP does not guarantee delivery — no connection setup or acknowledgment.
* Used primarily when fast updates are more important than guaranteed delivery.

---

## 8.1.6 Example 3 — Using Unity Netcode for GameObjects to Start a Host and Client

Unity’s Netcode package simplifies multiplayer networking by handling much of the low-level transport and state synchronization automatically.

```csharp
using Unity.Netcode;
using UnityEngine;

public class NetworkStartup : MonoBehaviour
{
    public void StartHost()
    {
        NetworkManager.Singleton.StartHost();
        Debug.Log("Host started!");
    }

    public void StartClient()
    {
        NetworkManager.Singleton.StartClient();
        Debug.Log("Client connected!");
    }
}
```

### Explanation:

* The **Host** acts as both the server and a local player.
* The **Client** connects to the host to participate in the game.
* Unity Netcode handles connection lifecycle, messaging, and state sync.

---

## 8.1.7 Architecture Diagram: Client-Server Model in Unity Netcode

*(Imagine a simple diagram here)*

* **Host/Server**: Maintains authoritative game state, listens for client updates.
* **Clients**: Send inputs and receive game state updates.
* **NetworkManager**: Orchestrates connections, RPCs, and synchronization.

---

## Summary

* XR multiplayer games rely on efficient and low-latency networking.
* TCP ensures reliable data delivery, UDP prioritizes speed and low latency.
* Unity abstracts much complexity with its Transport Layer and Netcode.
* Simple TCP and UDP examples in Unity demonstrate fundamental networking concepts.
* Unity Netcode allows easy setup of hosts and clients, handling synchronization behind the scenes.

---

## Exercises

1. Modify the TCP client example to receive and display a response from the server.
2. Extend the UDP client to periodically send position data (simulate XR headset tracking).
3. Use Unity Netcode to start a host and multiple clients, then observe connection logs.

---

Great! Let’s move on to the next section with the same detailed, textbook-style approach:

---

# **8.2 Multiplayer in Unity: Coding Skills & Examples**

---

## Introduction

Multiplayer XR experiences require players to see and interact with each other’s avatars, share object states, and synchronize actions in real time. This section dives deeper into **how to implement multiplayer gameplay in Unity using Netcode for GameObjects**, focusing on syncing player movement, XR headset and controller tracking, and handling network ownership of objects.

By the end of this section, you will be able to:

* Implement synchronized player movement in a multiplayer environment
* Sync XR-specific transforms (headset and controllers) over the network
* Manage ownership and control of networked objects
* Understand key concepts such as `IsOwner`, RPCs, and network variable syncing

---

## 8.2.1 Synchronizing Player Movement

In multiplayer games, each player controls their avatar. It’s crucial that movement inputs only affect the local player and that the resulting position updates are propagated to all other players.

---

### Key Concept: `IsOwner`

In Unity Netcode, each networked object has an owner — the client who controls it. The `IsOwner` property ensures that only the owner processes input and sends updates for that object.

---

### Example: Player Movement Script

```csharp
using Unity.Netcode;
using UnityEngine;

public class PlayerMovement : NetworkBehaviour
{
    public float speed = 3f;

    void Update()
    {
        // Only allow the player who owns this object to move it
        if (!IsOwner) return;

        float moveX = Input.GetAxis("Horizontal");
        float moveZ = Input.GetAxis("Vertical");

        Vector3 move = new Vector3(moveX, 0, moveZ) * speed * Time.deltaTime;
        transform.Translate(move);
    }
}
```

#### Explanation:

* The `Update()` method processes input **only if the client owns the player object**.
* Movement is local, but position updates are automatically synchronized by Unity Netcode's `NetworkTransform` component attached to the same GameObject (see next section).

---

## 8.2.2 Syncing Transform Data with `NetworkTransform`

Unity Netcode offers the `NetworkTransform` component to synchronize position, rotation, and scale of networked objects automatically.

---

### Setup:

* Attach `NetworkTransform` to your player prefab along with `NetworkObject`.
* This handles sending transform updates from the owner to all other clients.

---

### Why Use `NetworkTransform`?

* Prevents jitter by interpolating movement on remote clients.
* Efficiently compresses and sends transform data only when necessary.
* Supports configurable update rates to optimize bandwidth.

---

## 8.2.3 Synchronizing XR Headset and Controllers

XR avatars require real-time synchronization of headset and controller positions and rotations so other players see accurate body and hand movements.

---

### Example: Sync XR Headset Transform

```csharp
using Unity.Netcode;
using UnityEngine;

public class XRHeadSync : NetworkBehaviour
{
    public Transform vrHeadset; // Assign in inspector or dynamically

    void Update()
    {
        if (IsOwner)
        {
            // Update networked transform with local headset data
            transform.position = vrHeadset.position;
            transform.rotation = vrHeadset.rotation;
        }
    }
}
```

---

### Explanation:

* The local player continuously updates their networked object’s position and rotation to match the real headset.
* Other players automatically receive these updates and see the correct head movement in their view.

---

## 8.2.4 Syncing Controller Data

You can apply the same concept to controllers:

* Create networked objects representing left and right controllers.
* Use `NetworkTransform` or custom syncing scripts to update their position and rotation.

---

### Advanced: Network Variables for Fine-Grained Control

Sometimes, you want to sync more than just transforms — like button presses, gestures, or grabbing states. For this, Unity Netcode offers **NetworkVariables**.

---

### Example: Syncing a Boolean Grab State

```csharp
using Unity.Netcode;
using UnityEngine;

public class XRControllerSync : NetworkBehaviour
{
    // NetworkVariable to sync grabbing state
    public NetworkVariable<bool> isGrabbing = new NetworkVariable<bool>(false);

    void Update()
    {
        if (IsOwner)
        {
            // Update grabbing state based on input
            bool grabbing = Input.GetButton("Fire1"); // Example button
            isGrabbing.Value = grabbing;
        }
        else
        {
            // React to grabbing state changes on remote clients
            if (isGrabbing.Value)
            {
                // Play grab animation or enable object attachment
            }
            else
            {
                // Release object
            }
        }
    }
}
```

---

## 8.2.5 Ownership and Object Authority

Ownership determines who controls the object and can send updates for it.

* By default, the player who spawns an object owns it.
* Ownership can be transferred at runtime (e.g., when a player grabs a shared object).

---

### Transferring Ownership Example

```csharp
using Unity.Netcode;
using UnityEngine;

public class OwnershipTransfer : NetworkBehaviour
{
    public void TransferOwnership(ulong newOwnerClientId)
    {
        if (IsServer)
        {
            NetworkObject.ChangeOwnership(newOwnerClientId);
            Debug.Log($"Ownership transferred to client {newOwnerClientId}");
        }
    }
}
```

---

## 8.2.6 Example Project Walkthrough: Multiplayer XR Avatar Sync

### Objective

* Synchronize player movement, headset, and controller transforms for multiple XR players.

### Steps

1. Create prefab with:

   * `NetworkObject` component
   * `NetworkTransform` on root (for player position)
   * Child objects for VR headset and controllers with `NetworkTransform` or custom syncing scripts.

2. Use scripts like `PlayerMovement`, `XRHeadSync`, and `XRControllerSync`.

3. Start Host and multiple Clients via `NetworkManager`.

4. Test by moving and grabbing objects, verifying smooth sync.

---

## 8.2.7 Architecture Diagram: Multiplayer XR Avatar Sync Flow

*(Diagram to include:)*

* Owner client updates headset & controllers locally
* Updates sent over network via `NetworkTransform` and `NetworkVariables`
* Remote clients receive updates and interpolate avatar movements
* Server maintains authoritative state, manages ownership transfers

---

## Summary

* Use `IsOwner` to ensure only local players control their avatars.
* Attach `NetworkTransform` to sync position and rotation efficiently.
* Use `NetworkVariables` for syncing states beyond transforms.
* Manage ownership carefully for interactions like grabbing and releasing objects.
* Combining these tools enables smooth, realistic multiplayer XR avatar experiences.

---

## Exercises

1. Implement a simple networked avatar with synced head and controller positions using `NetworkTransform`.
2. Add NetworkVariable syncing for a simple hand gesture (e.g., a "point" or "fist" boolean).
3. Write a script to transfer ownership of a networked object when a player grabs it.

---

# **8.3 Vibe Coding for Multiplayer XR**

---

## Introduction

Developing multiplayer XR games can be complex and time-consuming. “**Vibe coding**” is an innovative approach that leverages Large Language Models (LLMs), such as ChatGPT, to **rapidly prototype multiplayer features** by translating plain-language descriptions into working code snippets.

In this section, you will learn:

* What vibe coding is and why it matters for multiplayer XR development
* How to craft effective prompts to get useful code from LLMs
* Examples of vibe coding applied to common multiplayer XR scenarios
* How to integrate AI-generated code into your Unity projects safely and efficiently

---

## 8.3.1 What Is Vibe Coding?

**Vibe coding** is the process of collaborating with AI-powered language models to generate, debug, or improve code by describing desired functionality in natural language.

**Benefits include:**

* Accelerated prototyping, saving time on boilerplate and repetitive tasks
* Access to expert-level coding patterns and best practices
* Inspiration for novel feature ideas or problem-solving approaches
* Learning through code explanations generated by the AI

---

## 8.3.2 The Vibe Coding Workflow for Multiplayer XR

1. **Define the Feature in Plain Language:**
   Clearly describe what you want the multiplayer system or feature to do.
   *Example:* “Create Unity Netcode code to sync a VR player’s head and hand movements across clients.”

2. **Input the Prompt into an LLM:**
   Use ChatGPT or similar tools and submit your prompt.

3. **Review and Test the Generated Code:**
   Always validate and test AI-generated code carefully to ensure correctness and security.

4. **Integrate and Customize:**
   Adapt the output to your project’s specifics and combine it with existing systems.

5. **Iterate:**
   Refine prompts or ask the AI to extend or fix code as needed.

---

## 8.3.3 Crafting Effective Prompts

Good prompts are clear, concise, and contain essential context. Include:

* The development platform (e.g., Unity, C#)
* The multiplayer framework (e.g., Unity Netcode for GameObjects)
* The specific multiplayer behavior or system you want
* Any constraints or special requirements (e.g., VR controllers, physics sync)

**Example prompt:**

> “Generate C# code for Unity Netcode that lets two VR players throw a ball to each other, syncing physics and ownership.”

---

## 8.3.4 Example 1: AI-Generated Code for a Networked Throwing Ball

### AI Output:

```csharp
using Unity.Netcode;
using UnityEngine;

public class NetworkBall : NetworkBehaviour
{
    private Rigidbody rb;

    void Start() => rb = GetComponent<Rigidbody>();

    [ServerRpc]
    public void ThrowBallServerRpc(Vector3 force)
    {
        rb.AddForce(force, ForceMode.Impulse);
        // Sync the new velocity to clients
        UpdateVelocityClientRpc(rb.velocity);
    }

    [ClientRpc]
    private void UpdateVelocityClientRpc(Vector3 velocity)
    {
        if (!IsServer)
        {
            rb.velocity = velocity;
        }
    }
}
```

### Explanation:

* `ThrowBallServerRpc` is called by the client to request the server to apply force.
* Server applies physics force to the ball rigidbody.
* `UpdateVelocityClientRpc` syncs the updated velocity to all clients for consistent physics.
* Ownership remains with the server to keep authority over physics.

---

## 8.3.5 Example 2: Syncing XR Player Input Using Vibe Coding

**Prompt:**

> “Generate Unity Netcode code to sync a VR player’s headset and hand positions, updating transforms only when changed.”

---

### AI-Generated Code:

```csharp
using Unity.Netcode;
using UnityEngine;

public class XRPlayerSync : NetworkBehaviour
{
    public Transform head;
    public Transform leftHand;
    public Transform rightHand;

    private NetworkVariable<Vector3> headPos = new NetworkVariable<Vector3>();
    private NetworkVariable<Quaternion> headRot = new NetworkVariable<Quaternion>();
    private NetworkVariable<Vector3> leftHandPos = new NetworkVariable<Vector3>();
    private NetworkVariable<Quaternion> leftHandRot = new NetworkVariable<Quaternion>();
    private NetworkVariable<Vector3> rightHandPos = new NetworkVariable<Vector3>();
    private NetworkVariable<Quaternion> rightHandRot = new NetworkVariable<Quaternion>();

    void Update()
    {
        if (IsOwner)
        {
            headPos.Value = head.position;
            headRot.Value = head.rotation;
            leftHandPos.Value = leftHand.position;
            leftHandRot.Value = leftHand.rotation;
            rightHandPos.Value = rightHand.position;
            rightHandRot.Value = rightHand.rotation;
        }
        else
        {
            head.position = headPos.Value;
            head.rotation = headRot.Value;
            leftHand.position = leftHandPos.Value;
            leftHand.rotation = leftHandRot.Value;
            rightHand.position = rightHandPos.Value;
            rightHand.rotation = rightHandRot.Value;
        }
    }
}
```

### Notes:

* Uses `NetworkVariable` for each position and rotation.
* Automatically syncs values across networked clients.
* Efficiently updates transforms only when changes occur.

---

## 8.3.6 Best Practices for Vibe Coding in Multiplayer XR

* **Test generated code in isolation first** to avoid introducing bugs.
* **Understand the code you receive** — don’t blindly copy-paste.
* **Keep AI prompts specific and scoped** to avoid overly generic outputs.
* **Iterate and refine** prompts based on AI responses.
* **Combine AI outputs with your existing architecture** for smooth integration.
* **Ensure security** — avoid exposing sensitive data or allowing unauthorized actions.

---

## 8.3.7 Integrating AI-Generated Code into Unity Projects

* Copy AI-generated scripts into your Unity project folder.
* Attach scripts to relevant GameObjects (e.g., avatars, networked objects).
* Set up required components like `NetworkObject`, `NetworkTransform`.
* Test multiplayer functionality in Editor with multiple clients or builds.
* Profile network traffic to ensure efficiency and smooth gameplay.

---

## Summary

* Vibe coding leverages AI to speed up XR multiplayer development.
* Clear, focused prompts are key to useful AI-generated code.
* AI can generate scripts for syncing movement, physics, ownership, and more.
* Always validate, test, and adapt AI code before production use.
* Combining vibe coding with manual expertise leads to efficient prototyping and development.

---

## Exercises

1. Use an LLM to generate a Unity Netcode script for a simple networked object pickup system, then test and integrate it.
2. Experiment with different prompts to improve AI output quality for syncing XR controller gestures.
3. Analyze AI-generated multiplayer code for security issues or inefficiencies.

---



## 8.3.8 Using Detailed Game Specifications to Generate Multiplayer XR Games with AI

One of the most powerful uses of vibe coding is to provide an AI model with a **complete, detailed game specification** and request it to generate starting code that matches the design. This section guides you on how to write such specifications and how to transform them into effective prompts.

---

### Why Detailed Specifications Matter

* **Clarity:** The AI can only generate what it understands. The more detailed and structured your specification, the better the results.
* **Scope control:** Helps avoid vague or overly generic outputs.
* **Reuse and iteration:** A well-written specification can be refined and reused with minor adjustments.

---

## 8.3.9 Example: Full Game Specification for a Multiplayer XR Ping Pong Game

---

### Game Title: **Multiplayer XR Ping Pong**

---

### Game Description:

A fast-paced multiplayer VR ping pong game where two players face off across a virtual table. Players use their VR controllers as paddles to hit a virtual ping pong ball back and forth. The game syncs player movements, paddle positions, and ball physics over the network to create a smooth competitive experience.

---

### Core Features:

1. **Multiplayer Setup:**

   * Supports exactly two players connected over Unity Netcode.
   * One player acts as Host, the other connects as Client.

2. **Player Representation:**

   * Each player’s VR headset and controllers are synced and represented as an avatar.
   * Paddles are attached to controller objects.

3. **Ball Physics:**

   * The ping pong ball uses Rigidbody physics synchronized across clients.
   * Ball ownership transfers to the last player who hit it.

4. **Gameplay Mechanics:**

   * Players swing paddles to hit the ball.
   * Ball speed increases gradually after each successful hit.
   * Scoring system tracks points; first to 11 wins.

5. **UI:**

   * Displays current scores for both players.
   * Shows game status (e.g., “Waiting for Player 2”, “Game Over”).

---

### Networking Details:

* **Synchronization:**

  * Player head and paddle positions updated via NetworkVariables.
  * Ball physics state synchronized via ServerRPCs and ClientRPCs.
* **Ownership:**

  * Server holds authority over the ball physics.
  * Ownership transfers when a player hits the ball.

---

## 8.3.10 Crafting a Prompt Based on the Specification

When instructing an AI to generate code for this game, your prompt should:

* Include the **development environment** (Unity, C#, Unity Netcode)
* Describe **core game mechanics and multiplayer features** precisely
* Specify **network synchronization requirements**
* Indicate any **special mechanics** like ownership transfer and scoring
* Optionally, request specific code modules or classes

---

### Example Prompt:

> “Generate C# scripts for Unity using Unity Netcode that implement a multiplayer VR ping pong game for two players. Each player’s VR headset and paddle (attached to the controller) positions should be synced using NetworkVariables. The ping pong ball should have Rigidbody physics synced via ServerRPCs, with ownership transferring to the player who hits the ball. Include logic to increase ball speed after each hit, and a scoring system tracking points up to 11. Also, include a simple UI to display scores and game status messages.”

---

## 8.3.11 AI-Generated Sample Output (Excerpt)

```csharp
using Unity.Netcode;
using UnityEngine;
using UnityEngine.UI;

public class PingPongBall : NetworkBehaviour
{
    private Rigidbody rb;
    public float speedIncrease = 0.5f;
    private NetworkVariable<Vector3> velocity = new NetworkVariable<Vector3>();

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void Update()
    {
        if (!IsServer)
        {
            rb.velocity = velocity.Value;
        }
    }

    [ServerRpc(RequireOwnership = false)]
    public void HitBallServerRpc(Vector3 force, ulong hitterClientId)
    {
        rb.AddForce(force, ForceMode.Impulse);
        velocity.Value = rb.velocity;
        ChangeOwnership(hitterClientId);
        IncreaseSpeed();
    }

    private void ChangeOwnership(ulong newOwner)
    {
        NetworkObject.ChangeOwnership(newOwner);
    }

    private void IncreaseSpeed()
    {
        rb.velocity *= (1 + speedIncrease);
        velocity.Value = rb.velocity;
    }
}

public class ScoreManager : NetworkBehaviour
{
    public NetworkVariable<int> player1Score = new NetworkVariable<int>(0);
    public NetworkVariable<int> player2Score = new NetworkVariable<int>(0);
    public Text scoreText;

    void Update()
    {
        scoreText.text = $"Player 1: {player1Score.Value}  Player 2: {player2Score.Value}";
    }

    [ServerRpc]
    public void AddPointServerRpc(int playerNumber)
    {
        if (playerNumber == 1)
            player1Score.Value++;
        else if (playerNumber == 2)
            player2Score.Value++;
    }
}
```

---

## 8.3.12 How to Use This Prompt and Output

* **Feed the prompt into ChatGPT** or another LLM and generate code modules.
* **Review the output** for logic, correctness, and completeness.
* **Integrate scripts into your Unity project**, attach components, and configure prefabs (e.g., paddle to controller).
* **Test multiplayer gameplay** with Host and Client builds to verify sync and interactions.
* **Iterate by refining the prompt** to add features or fix bugs.

---

## 8.3.13 Tips for Writing Better Prompts

| Tip                | Explanation                                     | Example                                                             |
| ------------------ | ----------------------------------------------- | ------------------------------------------------------------------- |
| Be Specific        | Describe the game rules and networking clearly. | “Ball physics synced with ServerRPCs and velocity updates”          |
| Define Roles       | Explain player roles and ownership.             | “Host manages ball ownership; ownership transfers on hit.”          |
| Mention Frameworks | State the development environment and tools.    | “Use Unity Netcode for GameObjects and C#.”                         |
| Include Edge Cases | Specify game end conditions or error handling.  | “First to 11 points wins; show game over message.”                  |
| Ask for Modularity | Request separate classes/scripts for features.  | “Generate separate scripts for Ball, ScoreManager, and PlayerSync.” |

---

## Summary

* Detailed game specifications form the foundation for effective vibe coding prompts.
* A well-structured prompt includes environment, game mechanics, networking details, and special features.
* AI can generate working code snippets for complex multiplayer XR games using these prompts.
* Testing, reviewing, and iterative refinement are key to successful integration.

---

## Exercises

1. Write a detailed game specification for a multiplayer XR co-op puzzle game.
2. Create a prompt from your specification and generate initial code with an LLM.
3. Compare outputs from different prompt variations and document what changes improve code quality.


