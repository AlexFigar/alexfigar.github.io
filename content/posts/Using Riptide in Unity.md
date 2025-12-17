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
Riptide Networking is a lightweight C# networking library primarily designed for use in multiplayer games. It can be used in Unity as well as in other .NET environments. Despite being lean it is very flexible and getting it setup is not as hard as it looks.

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

We will start by creating our own `NetworkManager.cs` using the singleton pattern and add a dictionary of players.
The `ushort` is the `clientID` riptide uses to keep track of clients and the [`GameObject`](https://docs.unity3d.com/6000.3/Documentation/Manual/class-GameObject.html) will be our instantiated player.

We will also declare some references to riptides server and client.
```C#{lineos=true,hl_lines=[10,11,13]}
using UnityEngine;
using System.Collections.Generic;
using Riptide;

public class NetworkManager : MonoBehaviour
{
    public static NetworkManager _instance;
    public static NetworkManager Instance { get { return _instance; } }

    public Server server;
    public Client client;

    public Dictionary<ushort, GameObject> playerList;

    void Awake()
    {
        if (_instance != null && _instance != this)
        {
            Destroy(gameObject);
        } else {
            _instance = this;
        }
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
Create a static class called `ServerBehaviour.cs` This is the class where all of out server messages and message handlers will reside.

The message handler attribute with our message ID cast to a ushort highlighted below is how we link our message handlers to incoming messages.
```C#{lineos=true,hl_lines=[14]}
using Riptide;
using UnityEngine;

public static class ServerBehaviour
{
    #region Messages
    public static void SendDataToClient(ushort clientID)
    {
        Debug.Log("Sending Data to "+clientID);
    }
    #endregion

    #region Message Handlers
    [MessageHandler((ushort)MessageID.HelloServerImAClient)]
    public static void ReceiveDataFromClient(ushort fromClientID, Message message)
    {
        Debug.Log("Client "+fromClientID+" says hello");
    }
    #endregion
}
```
### Client Behaviour
Now its time for the client. Create a static class called `ClientBehaviour.cs`  Pretty similar setup just the opposite. Make sure everything is correct these classes look very similar at the moment.

```C#{lineos=true,hl_lines=[14]}
using Riptide;
using UnityEngine;

public static class ClientBehaviour
{
    #region Messages
    public static void SendDataToServer(ushort clientID)
    {
        Debug.Log("Sending data to server");
    }
    #endregion

    #region Message Handlers
    [MessageHandler((ushort)MessageID.HelloServerImAClient)]
    public static void ReceivedDataFromServer(Message message)
    {
        Debug.Log("Server says hello!");
    }
    #endregion
}
```

### Starting Server or Client
Ok so we need a way to actually start the server or client so we are going to revisit the `NetworkManager.cs` class and a few things.
```C#{lineos=true,hl_lines=[14]}

```

### Time to test!

Unity over the last few years have added some multiplayer tools to aid in this process. The tool we will be using is part of the "Multiplayer Play Mode" package which can be installed in the package manager window.

![](/riptide/MultiplayerPlaymodePackage.png)

Make sure that "Run in Background" is enabled in **project setting > player settings > resolution and presentation**.

![](/riptide/RunInBackground.png)