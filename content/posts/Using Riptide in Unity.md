---
author: Alex Figar
title: Using Riptide Networking
date: 2025-11-11
description: A short guide to getting riptide up and running in Unity with client and server logic in the same project!
tags:
  - Unity
  - Multiplayer
categories:
  - Programming
  - Tutorial
ShowToc: true
TocOpen: false
Draft: "0"
---
Riptide Networking is a lightweight C# networking library primarily designed for use in multiplayer games. It can be used in Unity as well as in other .NET environments. Despite being qlean it is very flexible and getting it setup is not as hard as it looks.

Code can be found at https://github.com/AlexFigar/RiptideStarter.

*This guide uses Unity-6000.3.0f1 and Riptide-2.2.1*
<!--more-->

## Installation
Getting this networking library into your unity project is as simple as popping into the package manager in Unity and installing the package via git URL:
1. In Unity, go to **Window > Package Manager**.
2. Click **Add** (the plus icon) > **Add package from git URL**.
3. Paste the URL above and click **Add**.
4. And enter the git URL: `https://github.com/RiptideNetworking/Riptide.git?path=/Packages/Core#unity-package`

![](/riptide/UPM2.png)

## Basic Implementation

As stated in the description of this library, riptide is a **lightweight** networking library. This means we need to do a little bit of legwork ourselves to get it up and running.

### Network Manager

We will start by creating our own `NetworkManager.cs` as a singleton,  declare our client and server and initialise riptides logger.



```C#{lineos=true,hl_lines=[11,12,22]}
using UnityEngine;
using System.Collections.Generic;
using Riptide;
using Riptide.Utils;

public class NetworkManager : MonoBehaviour
{
    public static NetworkManager _instance;
    public static NetworkManager Instance { get { return _instance; } }

    public Server server;
    public Client client;

    void Awake()
    {
        if (_instance != null && _instance != this)
        {
            Destroy(gameObject);
        } else {
            _instance = this;
        }
        RiptideLogger.Initialize(Debug.Log, Debug.Log, Debug.LogWarning, Debug.LogError, false);
        DontDestroyOnLoad(gameObject);
    }
}
```


### Message ID's
Riptide uses message id's (ushort) to determine what type of message is incoming and how it should be handled. So we are going to create an enum called MessageID in `MessageID.cs` to keep track of which ids are representative of which message type.

We will start with the first messages exchanged between client and server.

```C#{lineos=true,hl_lines=[3,4]}
public enum MessageID : ushort
{
    HelloServerImAClient = 0, //From the client => server
    HelloClientImAServer = 1 //From the server => client
}
```
### Server Behaviour
Create a static class called `ServerBehaviour.cs` This is the class where all of out SERVER messages and message handlers will reside.

Riptide uses object pooling so we do NOT create `new()` messages we use `Message.Create()` and parse in 

The message handler attribute with our message ID highlighted below is how we link our message handlers to incoming messages. 
```C#{lineos=true,hl_lines=[9, 16]}
using Riptide;
using UnityEngine;

public static class ServerBehaviour
{
    #region Messages
    public static void SendDataToClient(ushort clientID)
    {
        Message message = Message.Create(MessageSendMode.Reliable, MessageID.HelloClientImAServer);
        message.AddString("Hello Client!");
        NetworkManager.Instance.server.Send(message, clientID);
    }
    #endregion

    #region Message Handlers
    [MessageHandler((ushort)MessageID.HelloServerImAClient)]
    public static void ReceivedDataFromClient(ushort fromClientID, Message message)
    {
        Debug.Log("Client "+fromClientID+" says "+ message.GetString());
    }
    #endregion
}
```
### Client Behaviour
Now its time for the client. Create a static class called `ClientBehaviour.cs`  Pretty similar setup to the last class, just with swapped message ID's. Make sure everything is correct these classes do look very similar at the moment.

```C#{lineos=true,hl_lines=[17]}
using Riptide;
using UnityEngine;

public static class ClientBehaviour
{
    #region Messages
    public static void SendDataToServer()
    {
        Message message = Message.Create( MessageSendMode.Reliable, MessageID.HelloServerImAClient);
        message.AddString("Hello Server!");
        NetworkManager.Instance.client.Send(message);
    }
    #endregion

    #region Message Handlers
    //Handles the servers message Message ID 1
    [MessageHandler((ushort)MessageID.HelloClientImAServer)]
    public static void ReceivedDataFromServer(Message message)
    {
        Debug.Log("Server says "+ message.GetString());
    }
    #endregion
}
```

### Starting Server or Client
 are going to revisit the `NetworkManager.cs` class and create two new functions that will get called depending on if the game is running as a client or a server. 

`ClientConnected();` will be called on the server when a client connects. 
`ConnectedToServer();` will be called when the client successfully connects to a server.

```C#{lineos=true,hl_lines=[14]}
//Runs on Server
private void ClientConnected(object sender, ServerConnectedEventArgs e)
{
    ServerBehaviour.SendDataToClient(e.Client.Id);
}

//Runs on Client
private void ConnectedToServer(object sender, ClientConnectedEventArgs e)
{
    ClientBehaviour.SendDataToServer(client.Id);
}
```
We need a way to actually start the server or client so we will create two more functions `StartServer();`, `StartClient();` and while we are here hook the two other functions we created up to their respective events. [`Server.ClientConnected`](https://riptide.tomweiland.net/api/Riptide.Server.ClientConnected.html) and [`Client.Connected`](https://riptide.tomweiland.net/api/Riptide.Client.Connected.html)
```C#{lineos=true,hl_lines=[14]}
public void StartServer()
{
    server = new();
    server.ClientConnected += ClientConnected;
    server.Start(7777, 4);
}

public void StartClient()
{
    client = new();
    client.Connected += ConnectedToServer;
    client.Connect("127.0.0.1:7777");
}
```
*NOTE: Not to be confused with [`client.ClientConnected`](https://riptide.tomweiland.net/api/Riptide.Client.ClientConnected.html) which is called when a different client connects to the server you are ALREADY connected to.*
### Quick UI


### Time to test!

Unity over the last few years have added some multiplayer tools to aid in this process. The tool we will be using is part of the "Multiplayer Play Mode" package which can be installed in the package manager window.

![](/riptide/MultiplayerPlaymodePackage.png)

Make sure that "Run in Background" is enabled in **project setting > player settings > resolution and presentation**.

![](/riptide/RunInBackground.png)


## Spawning a Player
The `ushort` is the `clientID` riptide uses to keep track of clients and the [`GameObject`](https://docs.unity3d.com/6000.3/Documentation/Manual/class-GameObject.html) will be our instantiated player.
```

```