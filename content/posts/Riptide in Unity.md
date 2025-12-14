---
author: Alex Figar
title: Riptide Networking
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
Draft: "1"
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

## Implementation

As stated in the description of this library, riptide is a **lightweight** networking library. This means we need to do a little bit of legwork ourselves to get it up and running.

### Network Manager

We will start by creating our own `NetworkManager.cs` as a singleton and a dictionary of players.
The `ushort` is the `clientID` riptide uses to keep track of clients and the [`GameObject`](https://docs.unity3d.com/6000.2/Documentation/Manual/class-GameObject.html) will be our instantiated player.

```C#{lineos=true,hl_lines=[9]}
using UnityEngine;
using System.Collections.Generic;

public class NetworkManager : MonoBehaviour
{
    public static NetworkManager _instance;
    public static NetworkManager Instance { get { return _instance; } }

    public static Dictionary<ushort, GameObject> playerList = new();

    void Awake()
    {
        if (_instance != null && _instance != this)
        {
            Destroy(this.gameObject);
        } else {
            _instance = this;
        }
        DontDestroyOnLoad(this.gameObject);
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
This is the class where all of out server specific code will reside.
### Client Behaviour
### Time to test!

Unity over the last few years have added some multiplayer tools to aid in this process. The one we will be using is the "Multiplayer Play Mode" package which can be installed in the package manager window. 

![](/riptide/MultiplayerPlaymodePackage.png)

Make sure that "Run in Background" is enabled in **project setting > player settings > resolution and presentation**.

![](/riptide/RunInBackground.png)