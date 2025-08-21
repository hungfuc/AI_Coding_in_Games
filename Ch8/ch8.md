# Chapter 8: Networking and Multiplayer in XR Games


**Learning Objectives**

By the end of this chapter, you will be able to:

1. Understand fundamental networking concepts and architectures in XR multiplayer games.
2. Implement basic client-server and UDP communication in Unity.
3. Build multiplayer XR player movement synchronization using Unity Netcode.
4. Learn how to use “vibe coding” — an AI-assisted prompt-driven approach — to rapidly prototype multiplayer features.
5. Follow a complete, step-by-step example of creating a multiplayer XR game by writing detailed prompts and integrating AI-generated code.

**Key Concepts**

* Networking Models: Client-Server vs Peer-to-Peer
* TCP vs UDP transport protocols in multiplayer games
* Unity Netcode for GameObjects: high-level multiplayer framework
* Synchronizing player movement and XR-specific data (headsets/controllers)
* Ownership, authority, and networked object management
* Vibe Coding: Using AI prompt engineering to generate multiplayer game code
* Iterative prompt refinement and integration strategies



# 8.1 Networking Foundations in Unity

### 8.1.1 Networking Architectures

In multiplayer XR games, networking allows multiple players to share a virtual environment. Two common architectures are:

* **Client-Server:** One authoritative server manages game state; clients send inputs and receive updates.
* **Peer-to-Peer:** Players communicate directly without a central server; harder to synchronize but reduces server costs.

XR games often favor client-server models for consistent state and cheat prevention.



### 8.1.2 Transport Protocols

* **TCP:** Reliable, ordered, higher latency. Use for critical data.
* **UDP:** Unreliable, unordered, low latency. Use for frequent, fast updates like player movement.

Unity’s transport layer abstracts this, and frameworks like Netcode use UDP under the hood for performance.


#### Example: Simple TCP Client in Unity

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
            client = new TcpClient("127.0.0.1", 5000);
            stream = client.GetStream();
            Debug.Log("Connected to server!");

            // Send message
            string message = "Hello from Unity!";
            byte[] data = Encoding.ASCII.GetBytes(message);
            stream.Write(data, 0, data.Length);
        }
        catch (Exception e)
        {
            Debug.LogError("Connection error: " + e.Message);
        }
    }

    void OnApplicationQuit()
    {
        stream?.Close();
        client?.Close();
    }
}
```


#### Example: Simple UDP Client in Unity

```csharp
using System.Net;
using System.Net.Sockets;
using System.Text;
using UnityEngine;

public class SimpleUDPClient : MonoBehaviour
{
    private UdpClient udpClient;
    private IPEndPoint serverEndpoint;

    void Start()
    {
        udpClient = new UdpClient();
        serverEndpoint = new IPEndPoint(IPAddress.Parse("127.0.0.1"), 5001);
    }

    void Update()
    {
        // Send a position update every frame (example)
        Vector3 pos = transform.position;
        string message = $"POS:{pos.x},{pos.y},{pos.z}";
        byte[] data = Encoding.ASCII.GetBytes(message);
        udpClient.Send(data, data.Length, serverEndpoint);
    }

    void OnApplicationQuit()
    {
        udpClient.Close();
    }
}
```



#### Example: Starting Host and Client with Unity Netcode

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


## 8.2 Multiplayer XR Player Sync with Unity Netcode


### 8.2.1 Syncing Player Movement and Transforms

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



### 8.2.2 Syncing XR Headset and Controllers

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


### 8.2.3 Ownership and Authority Management

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


## 8.3 Vibe Coding: AI-Assisted Multiplayer XR Development


### 8.3.1 What Is Vibe Coding?

Vibe coding means describing game features or multiplayer behaviors in natural language prompts to an AI (like ChatGPT), which then generates usable Unity C# code. This accelerates prototyping by offloading boilerplate and complex networking logic to the AI.


### 8.3.2 Step-by-Step Prompt Approach to Build Multiplayer XR Games

To efficiently use vibe coding, break down your game into **small, manageable features** and craft clear prompts for each. Then iteratively refine the outputs.


**Step 1: Define Your Game Concept**

Write a short description of the game and its core multiplayer features.

*Example:*
“Create a two-player VR ping pong game where players use controllers to hit a ball, and all player movements and ball physics are synced.”


**Step 2: Generate Basic Networking Setup Code**

**Prompt:**

> Generate Unity Netcode C# scripts to start a host and client for a multiplayer XR game.

**AI Output:**

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


** Step 3: Generate Player Avatar Sync Code**

**Prompt:**

> Generate code syncing VR headset and controller transforms using NetworkVariables in Unity Netcode.

**AI Output:**

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

**Step 4: Generate Ball Physics and Interaction Code**

**Prompt:**

> Generate Unity Netcode C# code to create a networked ping pong ball with Rigidbody physics. Include server RPCs for applying forces and syncing velocity.

**AI Output:**

```csharp
using Unity.Netcode;
using UnityEngine;

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
```

**Step 5: Generate Ownership Transfer Logic**

Ownership transfer is included in the above `HitBallServerRpc` method by calling `ChangeOwnership`.


**Step 6: Generate Scoring and UI Logic**

**Prompt:**

> Generate a score manager using NetworkVariables for two players with a Unity UI text display.”8

**AI Output:**

```csharp
using Unity.Netcode;
using UnityEngine;
using UnityEngine.UI;

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

**Step 7: Integrate and Test**

* Import all AI-generated scripts into your Unity project.
* Create prefabs for player avatars, paddles, and ping pong ball.
* Attach scripts and required components (`NetworkObject`, `NetworkTransform`).
* Run Host and Client instances to verify multiplayer sync, ball physics, ownership transfer, and scoring UI.
* Debug and refine prompts if needed.


## Chapter Summary

* You learned fundamental networking architectures and transport protocols for multiplayer XR games.
* Unity Netcode provides high-level tools for syncing player movement and managing ownership.
* Vibe coding lets you describe game features in natural language and receive working Unity C# scripts.
* The step-by-step prompt approach breaks your game into modular features for efficient AI-assisted development.
* A complete multiplayer XR ping pong game was created by iteratively prompting the AI and integrating generated code.


## Review Questions

1. What is the role of `IsOwner` in Unity Netcode multiplayer scripts?
2. How does the client-server architecture benefit multiplayer XR games?
3. What are NetworkVariables and when should you use them?
4. Describe the process of using vibe coding to create a multiplayer game feature.
5. How can ownership transfer be implemented in Unity Netcode?


## Exercises

1. Use vibe coding to generate a Unity script for networked object grabbing and releasing.
2. Create detailed prompts for a cooperative XR puzzle game and generate code with an AI.
3. Analyze AI-generated code and improve it for performance or security.


