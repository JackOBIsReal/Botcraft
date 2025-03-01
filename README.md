![Linux](https://github.com/adepierre/Botcraft/workflows/Linux/badge.svg) ![Windows](https://github.com/adepierre/Botcraft/workflows/Windows/badge.svg)

# Botcraft

Botcraft is a cross-platform C++ library to connect and interact with Minecraft servers with (optional) integrated OpenGL renderer.

This is a "learn-by-doing" code, with some lines dating back to 2017 and never changed since. Some things are probably done (very) badly and should be refactored. Do not hesitate to open an issue or to send a PR if you know how to improve some part of it!

## Features

- Connection to minecraft server (both offline mode and online connection with Mojang or Microsoft account)
- DNS server name resolution with and without SRV record
- All official releases from 1.12.2 to 1.17.1 supported
- Compression
- Physics and collisions
- (Optional) Rendering of all the blocks (including entity-blocks like chests, banners...)
- Bot control with mouse and keyboard
- Path finding
- Block breaking
- Inventory managing
- Block placing
- Block interaction (button, lever etc...)

Example of pathfinding. Right of the screen is the integrated renderer
![](gifs/video.gif)

More complex example with 10 survival bots collaborating on a pixel art build. They are all in survival, so they have to pick the right blocks in the chests, eat food and obviously can't fly. There is no global supervision, and they can't communicate with each other. Better quality video in [this reddit post](https://www.reddit.com/r/Minecraft/comments/mwzm26/my_survival_bot_project_applied_to_map_pixelart/).
![](gifs/mapart.gif)

## Dependencies

All the libraries are included either directly or as submodules and are built locally automatically (if not already found on your system) so you don't have to install anything manually.

- [asio](https://think-async.com/Asio/)
- [nlohmann json](https://github.com/nlohmann/json)
- [zlib](https://github.com/madler/zlib) (optional, used only if compression is enabled on your server)
- [openssl](https://www.openssl.org/) (optional, used only if your server is in online mode)

Optional dependencies (needed only if you want to build the OpenGL renderer)
- [stb_image](https://github.com/nothings/stb)
- [glad](https://glad.dav1d.de/)
- [glfw](https://github.com/glfw/glfw)
- [glm](https://github.com/g-truc/glm)
- [imgui](https://github.com/ocornut/imgui)
- [rectpack2D](https://github.com/TeamHypersomnia/rectpack2D)

The code is cross-platform and is automatically built on both Windows with Visual 2019 and Linux at each push. It should also work on reasonably older versions of Visual Studio and macOS as well.

### ProtocolCraft

ProtocolCraft is a sublibrary of the botcraft repository. It is a full implementation of the minecraft protocol for all supported versions. It used to be based on the protocol description on the [Wiki](https://wiki.vg/Protocol). However, as it seems to no longer be up to date after 1.16.5, I transitioned it to be based on the [official source code mapping](https://www.minecraft.net/en-us/article/minecraft-snapshot-19w36a) provided by Mojang.

Transitionning from one protocol description to the other was a breaking change, as all the packets and many variable names were changed. But this should be easier to maintain and update in the future, as it is now directly based on the official game source code, instead of a third-party documentation.

## Building and Installation

To build the library for the latest version of the game with both encryption and compression support, but without OpenGL rendering support:
```
git clone https://github.com/adepierre/Botcraft.git
cd Botcraft
mkdir build
cd build
cmake -DGAME_VERSION=latest -DBOTCRAFT_BUILD_EXAMPLES=ON -DBOTCRAFT_COMPRESSION=ON -DBOTCRAFT_ENCRYPTION=ON -DBOTCRAFT_USE_OPENGL_GUI=OFF ..
make all
```

At this point, you should have all the examples compiled and ready to run. Plese note that you don't have to clone recursively or download and install the dependencies manually, cmake will automatically take care of these steps based on your build configuration and what is already installed on your machine. On Windows with Visual, you can also use cmake-gui and then compile the .sln directly from Visual.

You can check [this discussion](https://github.com/adepierre/Botcraft/discussions/45#discussioncomment-1142555) for an example of how to use botcraft with your own code.

There are several cmake options you can modify:
- GAME_VERSION [1.XX.X or latest]
- BOTCRAFT_BUILD_EXAMPLES [ON/OFF]
- BOTCRAFT_INSTALL_ASSETS [ON/OFF] Copy all the needed assets to the installation folder along with the library and executable
- BOTCRAFT_COMPRESSION [ON/OFF] Add compression ability, must be ON to connect to a server with compression enabled
- BOTCRAFT_ENCRYPTION [ON/OFF] Add encryption ability, must be ON to connect to a server in online mode
- BOTCRAFT_USE_OPENGL_GUI [ON/OFF] If ON, botcraft will be compiled with the OpenGL GUI enabled
- BOTCRAFT_USE_IMGUI [ON/OFF] If ON, additional information will be displayed on the GUI (need BOTCRAFT_USE_OPENGL_GUI to be ON)

## Examples

Examples can be found in the [Examples](Examples/) folder:
- [0_HelloWorld](Examples/0_HelloWorld): Connect to a server, sends Hello World! in the chat then disconnect
- [1_UserControlledExample](Examples/1_UserControlledExample): Best with GUI, mouse and keyboard controlled player. Can be used in a dummy world (without any server) to test things like physics or rendering
- [2_ChatCommandExample](Examples/2_ChatCommandExample): Simple bot that obey commands sent through vanilla chat. Known commands at this point:
  - pathfinding
  - disconnecting 
  - checking its sourroundings for spawnable blocks (useful if you want to check whether or not a perimeter is spawn proof)
  - placing a block
  - interacting with a block (lever, button ...)
- [3_SimpleAFKExample](Examples/3_SimpleAFKExample): Simple example to stay at the same position. Physics is not processed, chunks are not saved in memory to save RAM.
- [4_MapCreatorExample](Examples/4_MapCreatorExample): Much more complex example, with autonomous behaviour implemented to build a map based pixel art. Can be launched with multiple bot simultaneously. They can share their internal representation of the world to save some RAM, at the cost of slowing down if too many share the same. Only extensively tested on 1.16.5, but should work with minor to none adaptation on previous/older versions.

## Connection

If the server has the option online-mode: false, you can connect with any username.

If the server has the option online-mode: true, you can connect with a Mojang account (login+password) or with a Microsoft account (empty login and password). For Microsoft account, you'll need to authenticate in your browser the first time. Then, credentials are stored in a file so you don't need to relog into your account everytime. Only the access token is saved in the file, Botcraft has never access to your Microsoft account password.

## To-do list

It's only a free time project, but there are still a lot of things to do! Right now the only usecase is an AFK bot to activate a farm with almost 0% usage of CPU/GPU. The next step is to add some functionalities like entities handling/rendering, attacking, crafting... Everything needed to automate more tasks in survival vanilla Minecraft!

There are also some minor improvements to existing features that have to be done:
- Improve scafholding/ladder physics (the player can't climb the ladders)
- Improve pathfinding with real colliders (the pathfinding considers every solid block as box of size 1 but the collisions are computed with the real shapes which lead to bad situations, for example with fences)

## License

GPL v3
