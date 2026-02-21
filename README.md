# Minecraft Bedrock on Linux
A guide to running Minecraft Bedrock on Linux using wineGDK and GDK-Proton




## Contents
- [Resources](#resources)
- [Overview](#overview)
- [Guide](#guide)
- [Debugging](#debugging)




## Resources
- [WineGDK Github](https://github.com/Weather-OS/WineGDK)
- [GDK-Proton Github](https://github.com/Weather-OS/GDK-Proton)
- [JutoTV's Video Guide](https://www.youtube.com/watch?v=m76O2cRIEnM)
- [Game Launchers Reverse Engineering Discord Server](https://discord.gg/RQtZUuHP)




## Overview
During Minecraft Bedrock for Windows version 1.21.120, the app was changed from UWP to GDK.  While UWP apps on Windows are nearly impossible to run on Linux, GDK apps are more possible.  People such as [Weather-OS](https://github.com/Weather-OS) have been working on tools to make GDK apps work under Wine.

### Software Explanations
- [Wine](https://www.winehq.org/) is a compatibility layer to run Windows apps on Linux.
- [Proton](https://github.com/ValveSoftware/Proton) is a wrapper for Wine made by Valve that includes various patches and libraries primarily for gaming.
- [WineGDK](https://github.com/Weather-OS/WineGDK) is a version of Wine that includes components for running GDK applications.
- [GDK-Proton](https://github.com/Weather-OS/GDK-Proton) is a "Protonified" version of Wine-GDK, meaning that it merges everything in Proton with everything in WineGDK.
- ```
  Wine   ->  Proton
  |           |
  v           v
  WineGDK -> GDK-Proton
  ```
- [ProxyPass](https://github.com/Kas-tle/ProxyPass/) proxies Minecraft Bedrock traffic through a LOCAL proxy running on your machine and allows Minecraft Bedrock within GDK-Proton to attach.  The flow is as follows:
    - ProxyPass authenticates with Microsoft's servers to sign in as your account
    - ProxyPass connects to the game server (ex. play.CubeCraft.net)
    - ProxyPass hosts a LAN world
    - Minecraft Bedrock joins the LAN world
    - ProxyPass uses the LAN connection to act as a relay.  It sends the server's packets to Minecraft and sends the player's world interactions to the server, while pretending to be the authenticated player.
- [Heroic](https://heroicgameslauncher.com/) is a game launcher that nicely makes it easy to run Minecraft Bedrock with the proper version of GDK-Proton.

### Limitations
- At present, online functionality is limited.  However, [ProxyPass](https://github.com/Kas-tle/ProxyPass/) as described above allows connections to multiplayer servers.
- Due to piracy concerns, obtaining game binaries requires having a Windows device or Windows VM to download Minecraft Bedrock on.




## Guide
This section is still being worked on.  The best advice is to:
1.  follow [JutoTV's Video Guide](https://www.youtube.com/watch?v=m76O2cRIEnM)

for 26.0+:
 
2. "copy windows.ui.dll from a working wine (or WineGDK) build to your GDK-Proton prefix" ([Weather-OS](https://discord.com/channels/1123890623586504714/1123953698440220672/1468067338006958271)).
3. "install the msi package located under Installers/GameInputRedist.msi within the game's binaries" ([Weather-OS](https://discord.com/channels/1123890623586504714/1168191388807073875/1450392912973140120)).




## Debugging
This section is still being worked on.

### Finding options.txt
Heroic users: `/home/$USER/Games/Heroic/Prefixes/default/GAMENAME/drive_c/users/steamuser/AppData/Roaming/Minecraft Bedrock/Users/Shared/games/com.mojang/minecraftpe/options.txt` ([JutoTV](https://discord.com/channels/1123890623586504714/1438901715628851250/1449336060424486964)).

### Connection time out when joining servers
In options.txt, turn `do_not_show_multiplayer_online_safety_warning` to 1 ([AnnoyingRains](https://discord.com/channels/1123890623586504714/1438901715628851250/1464916667728396289)).

### Infinite panorama loading screen
Use [JutoTV's options.txt](https://discord.com/channels/1123890623586504714/1438901715628851250/1449335380259246225).

### Slow responsivness
In options.txt, turn `gfx_vsync` to 0 ([SineVector](https://discord.com/channels/1123890623586504714/1438901715628851250/1440126243021258963)).