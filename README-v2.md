# Minecraft server optimization guide

Note for users that are on vanilla, Fabric or Spigot (or anything below Paper) - go to your server.properties and change `sync-chunk-writes` to `false`. This option is forcibly set to false on Paper and its forks, but on other server implementations you need to switch this to false manually. This allows the server to save chunks off the main thread, lessening the load on the main tick loop.

Guide for version 1.19. Some things may still apply to 1.15 - 1.18.

Based on [this guide](https://www.spigotmc.org/threads/guide-server-optimization%E2%9A%A1.283181/) and other sources (all of them are linked throughout the guide when relevant).

Use the table of contents located above (next to `README.md`) to easily navigate throughout this guide.

# Intro
There will never be a guide that will give you perfect results. Each server has their own needs and limits on how much you can or are willing to sacrifice. Tinkering around with the options to fine tune them to your servers needs is what it's all about. This guide only aims to help you understand what options have impact on performance and what exactly they change. If you think you found inaccurate information within this guide, you're free to open an issue or set up a pull request to correct it.

# Preparations

## Server JAR
Your choice of server software can make a huge difference in performance and API possibilities. There are currently multiple viable popular server JARs, but there are also a few that you should stay away from for various reasons.

Recommended top picks:
* [Paper](https://github.com/PaperMC/Paper) - The most popular server software that aims to improve performance while fixing gameplay and mechanics inconsistencies.
* [Pufferfish](https://github.com/pufferfish-gg/Pufferfish) - Paper fork that aims to further improve server performance.
* [Purpur](https://github.com/PurpurMC/Purpur) - Pufferfish fork focused on features and the freedom of customization.

You should stay away from:
* Any paid server JAR that claims async anything - 99.99% chance of being a scam.
* Bukkit/CraftBukkit/Spigot - Extremely outdated in terms of performance compared to other server software you have access to.
* Any plugin/software that enables/disables/reloads plugins on runtime. See [this section](#plugins-enablingdisabling-other-plugins) to understand why.
* Many forks further downstream from Pufferfish or Purpur will encounter instability and other issues. If you're seeking more performance gains, optimize your server or invest in a personal private fork.

## Map pregen
Map pregeneration, thanks to various optimizations to chunk generation added over the years is now only useful on servers with terrible, single threaded, or limited CPUs. Though, pregeneration is commonly used to generate chunks for world-map plugins such as Pl3xMap or Dynmap.

If you still want to pregen the world, you can use a plugin such as [Chunky](https://github.com/pop4959/Chunky) to do it. Make sure to set up a world border so your players don't generate new chunks! Note that pregenning can sometimes take hours depending on the radius you set in the pregen plugin. Keep in mind that with Paper and above your tps will not be affected by chunk loading, but the speed of loading chunks can significantly slow down when your server's cpu is overloaded.

It's key to remember that the overworld, nether and the end have separate world borders that need to be set up for each world. The nether dimension is 8x smaller than the overworld (if not modified with a datapack), so if you set the size wrong your players might end up outside of the world border!

**Make sure to set up a vanilla world border (`/worldborder set [diameter]`), as it limits certain functionalities such as lookup range for treasure maps that can cause lag spikes.**

# Configurations

## [server.properties]

### Networking


##### network-compression-threshold

`Good starting value: 256`

This allows you to set the cap for the size of a packet before the server attempts to compress it. Setting it higher can save some CPU resources at the cost of bandwidth, and setting it to -1 disables it. Setting this higher may also hurt clients with slower network connections. If your server is in a network with a proxy or on the same machine (with less than 2 ms ping), disabling this (-1) will be beneficial, since internal network speeds can usually handle the additional uncompressed traffic.




















[server.properties]: https://minecraft.fandom.com/wiki/Server.properties