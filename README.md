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
- [Game Launchers Reverse Engineering Discord Server](https://discord.gg/HD8bXfqFJf)




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
- [mingw-w64-x86_64-curl](https://mirror.msys2.org/mingw/mingw64/mingw-w64-x86_64-curl-8.17.0-1-any.pkg.tar.zst) helps with online functionality somehow.  I do not know how this works, and more information on it and where it comes from would be greatly appreciated.
- [https://curl.se/ca/cacert.pem](https://curl.se/ca/cacert.pem) provides SSL certificates.  I don't know why they come from here, and more information on it would be greatly appreciated.

### Limitations
- At present, online functionality is limited.  However, [ProxyPass](https://github.com/Kas-tle/ProxyPass/) as described above allows connections to multiplayer servers.
- Due to piracy concerns, obtaining game binaries requires having a Windows device or Windows VM to download Minecraft Bedrock on.




## Guide
Steps 1-6 follow [JutoTV's Video Guide](https://www.youtube.com/watch?v=m76O2cRIEnM) from 0:00-10:21.
1. Get game binaries and put them in what I'll call the `MCBE-dir`.
    1. Using either a Windows machine or a Windows VM, install Minecraft Bedrock and run it once.
    2. Open the game folder, very likely to be `C:\XboxGames\Minecraft for Windows`, then navigate to /Content.  ex. `C:\XboxGames\Minecraft for Windows\Content`
    4. From /Content, MOVE Minecraft.Windows.exe to the Desktop.
    5. COPY AND PASTE Minecraft.Windows.exe from the Desktop into the /Content folder.
    6. Move all the files within the /Content folder to your linux machine's `MCBE-dir/Content/` directory.  USB drives are an easy way to this when dual booting, moving between devices, or using a VM.
2. Add the certs to your MCBE-dir folder.
    - These are the steps at the [bottom of the GDK-Proton README](https://github.com/Weather-OS/GDK-Proton#for-minecraft).
    1. Download and extract [mingw-w64-x86_64-curl](https://mirror.msys2.org/mingw/mingw64/mingw-w64-x86_64-curl-8.17.0-1-any.pkg.tar.zst).
    2. Replace `MCBE-dir/Content/Xcurl.dll` with `mingw64/bin/libcurl-4.dll` from the extracted folder.
    3. Download SSL certificates using [https://curl.se/ca/cacert.pem](https://curl.se/ca/cacert.pem).
    4. Place the downloaded file at `MCBE-dir/etc/ssl/certs/ca-bundle.crt`.
3. Install [Heroic](https://heroicgameslauncher.com/).
4. Download and set up Proton-GDK for use within Heroic.
    1. Download the [latest version of GDK-Proton](https://github.com/Weather-OS/GDK-Proton/releases).
    2. Extract and move the folder into `/home/$USER$/.config/heroic/tools/proton/`.  This means that the contents(like `filelock.py`, `files`/, `protonFixes/`, `version`, and `proton`) are contained within a directory like `/home/$USER$/.config/heroic/tools/proton/GDK-Proton10-29/`.
5. Add the game within Heroic.
    1. Open the Heroic library and select "Add Game."
    2. Fill in the information:
        - Game/App Title: `Minecraft GDK` is recommended
        - App Image: Should Autofill if "Minecraft-GDK" is selected above
        - Select Platform Version to Install: `Windows`
        - Show Wine settings
            - Use Default Wine Settings: unchecked
            - WinePrefix: default is fine
            - Wine version: Select the name of the folder created in step 4.  For example, you might select `GDK-Proton10-29`.
        - Under "Select Executable," choose `MCBE-dir/Content/Minecraft.Windows.exe`.
    3. Press finish.
6. Choose proper Wine prefixes within Heroic.
    1. Open the settings for the game in Heroic.
    2. Navigate to Wine and find the Wine Prefix section.
    3. The following are required:
        - Enable Wine-Wayland: checked

**Steps for 26.0+:**

7. "Copy windows.ui.dll from a working wine (or WineGDK) build to your GDK-Proton prefix" ([Weather-OS](https://discord.com/channels/1123890623586504714/1123953698440220672/1468067338006958271)).
    1. Install Wine from https://gitlab.winehq.org/wine/wine/-/wikis/Download.
    2. Copy `/home/$USER$/.wine/drive_c/windows/system32` to `/home/$USER$/Games/Heroic/Prefixes/default/GAMENAME/drive_c/windows/system32`.
8. "Install the msi package located under Installers/GameInputRedist.msi within the game's binaries" ([Weather-OS](https://discord.com/channels/1123890623586504714/1168191388807073875/1450392912973140120)).
    1. Open the settings for the game in Heroic.
    2. Navigate to Wine and click "run exe on prefix" at the very bottom.
    3. Select `MCBE-dir/Installers/GameInputRedist.msi`.

**End steps for 26.0+**

9. Install and run ProxyPass.  This follow [JutoTV's Video Guide](https://www.youtube.com/watch?v=m76O2cRIEnM) from [10:21-15:55](https://youtu.be/m76O2cRIEnM?t=621).
    1. Create a ProxyPass directory, perhaps in your home directory.
    2. [Download ProxyPass](https://github.com/Kas-tle/ProxyPass/releases) and move the .jar into the ProxyPass directory.
    3. Open the ProxyPass directory in your terminal and run `java -jar ProxyPass.jar`.
    4. Follow ProxyPass's prompts to sign into your Microsoft account.
    5. Edit the config.yml within the ProxyPass directory and restart ProxyPass (`java -jar ProxyPass.jar`) in your terminal to change the server you're connected to.
        - To edit the config.yml, locate `destination:`.  On the indented lines following this, `host:` and `port:` allow you to edit the server you'll connect to.




## Debugging
This section is still being worked on.

### Finding options.txt
Heroic users: `/home/$USER$/Games/Heroic/Prefixes/default/GAMENAME/drive_c/users/steamuser/AppData/Roaming/Minecraft Bedrock/Users/Shared/games/com.mojang/minecraftpe/options.txt` ([JutoTV](https://discord.com/channels/1123890623586504714/1438901715628851250/1449336060424486964)).

### Connection time out when joining servers
In options.txt, turn `do_not_show_multiplayer_online_safety_warning` to 1 ([AnnoyingRains](https://discord.com/channels/1123890623586504714/1438901715628851250/1464916667728396289)).

### Infinite panorama loading screen
In options.txt, turn `do_not_show_multiplayer_online_safety_warning` to 1 ([JutoTV](https://www.youtube.com/watch?v=m76O2cRIEnM)).

### Slow responsivness
In options.txt, turn `gfx_vsync` to 0 ([SineVector](https://discord.com/channels/1123890623586504714/1438901715628851250/1440126243021258963)).