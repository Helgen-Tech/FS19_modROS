# FS19_modROS

## Landfill compactor and Bulldozer mods

The mods for the landfill and bulldozer compactors are available in our [CAT 836K](https://github.com/Helgen-Tech/CAT_836K_Landfill_EIFFAGE) and [NMC D11](https://github.com/Helgen-Tech/FS19_nmcD11Bulldozer) repositories.

To use any of them download the folder and put it into the mods directory of the farming simulator.

Make sure to buy the desired vehicle from the shop in game and sell all other vehicles to use it with the mod (in order to prevent several velocity commands to be sent to different vehicles).

For more details, follow the instructions available in the respositories.

## Development status

As of 2021-12-08, this mod is not under active development any more.

It will most likely remain functional (as FarmSim19 is not expected to change significantly), but it's not feature complete (see the issue tracker for open issues).


## Overview

This mod for Farming Simulator 2019 allows autonomous driving of FarmSim vehicles with the ROS navigation stack.

The mod itself is hosted in this repository.
[Helgen-Tech/fs_mod_ros_windows](https://github.com/Helgen-Tech/fs_mod_ros_windows) contains the companion Python scripts which implement a bridge between `modROS` and ROS 1 (using `rospy`).

[Helgen-Tech /fs_mod_ros](https://github.com/Helgen-Tech/fs_mod_ros) contains two example ROS packages which show how to interact with FarmSim19 running `modROS`.


### ROS message support

In its current state, `modROS` publishes four types of messages:

 - `rosgraph_msgs/Clock`: in-game simulated clock. This message stops being published when the game is paused/exited
 - `nav_msgs/Odometry`: ground-truth `Pose` and `Twist` of vehicles based on their in-game position and orientation
 - `sensor_msgs/LaserScan`: data from five virtual laser scanners (parallel planes) (but see [#10](https://github.com/tud-cor/FS19_modROS/issues/10))
 - `sensor_msgs/Imu`: from a simulated IMU (but see [#6](https://github.com/tud-cor/FS19_modROS/issues/6))

In addition, there is preliminary support for input into Farming Simulator:

 - `geometry_msgs/Twist`: controls the currently active vehicle (used by the demos in [tud-cor/fs_mod_ros](https://github.com/tud-cor/fs_mod_ros)). Behaviour is similar to - but not identical to - a regular ROS `Twist` interface to a vehicle (velocity mapping is not 1-to-1 yet due to how FarmSim controls vehicles)

Support for additional message types may be added in the future, but is limited by what the Farming Simulator Lua API exposes.


## ⚠️ Warning and disclaimer

`modROS` uses memory modification techniques which *could* be considered harmful by anti-cheat systems which are often included in games with on-line gameplay.

While the manifest clearly marks `modROS` as incompatible with multiplayer game sessions, and the authors have not experienced any problems so far, we feel we must warn users and strongly recommend against ever trying to enable the mod in multiplayer games, nor run any of the scripts in [Helgen-Tech/fs_mod_ros_windows](https://github.com/Helgen-Tech/fs_mod_ros_windows) while playing on-line.
Users cannot hold the authors of `modROS` responsible for loss of (on-line or other) functionality in Farming Simulator 19, nor for any damages related to or as a consequence of such loss of functionality.


## Status

Some parts are still under development.


## Requirements

* Windows 10
* Farming Simulator 19

Both the Steam version of Farming Simulator and the version distributed as a stand-alone purchase have been tested.


## Building

#### Installing the mod in Farming Simulator 19

__Note: The following instructions assume the default paths to various system folders are used on your machine. If not, please update the paths in the relevant places.__

1. Clone the mod or download the `.zip` at any location

    ```cmd
    git clone https://github.com/Helgen-Tech/FS19_modROS modROS
    ```

2. Moving mod files to the `mods` directory: 

__Note: You must launch the game at least once for the mods folder to be visible.__

In order for FarmSim to detect `modROS`, you either have to move the folder [modROS](https://github.com/Helgen-Tech/FS19_modROS) to the `mods` directory (`%USERPROFILE%\Documents\My Games\FarmingSimulator2019\mods`) or create a symbolic link from the `modROS` folder and drop it in the `FarmingSimulator2019\mods` directory.

    The authors have used [hardlinkshellext/linkshellextension](https://schinagl.priv.at/nt/hardlinkshellext/linkshellextension.html) which makes this an easy process.

3. If you've sucessfully installed the mod, it should be listed on the *Installed* tab of the *Mods* section in FarmSim (`modROS` is shown with a red square):

    ![modROS](.imgs/modROS.png)

    More information on installing mods in Farming Simulator 2019 can be found [here](http://www.farmingsimulator19mods.com/how-to-install-farming-simulator-2019-19-mods/).

4. Enable the Developer Console in Farming Simulator. Tutorial can be found [here](https://www.youtube.com/watch?v=_hYbnu6nJsQ).


#### ROS nodes in Windows
To start transmitting messages between Farmsim and ROS, please first go to [fs_mod_ros_windows](https://github.com/Helgen-Tech/fs_mod_ros_windows). 

#### Optional
If you would like to have a simulated RGB camera (not required to run navigation stack in ROS) in Farsmsim using screen capture, please refer to [`d3dshot_screen_grabber`](https://github.com/tud-cor/d3dshot_screen_grabber) 


## Running

To publish data, you need to first run a ROS node-`all_in_one_publisher.py`. Please follow the instructions [here](https://github.com/Helgen-Tech/fs_mod_ros_windows#publishing-data).

**Note: Before proceeding to the next step, make sure you see *waiting for client from FarmSim19* in the cmd window**


Open Farming Simulator 19 and switch the in-game console to command mode by pressing the tilde key (<kbd>~</kbd>) twice. More information on how to use console command in game can be found [here](https://wiki.nitrado.net/en/Admin_Commands_for_Farming_Simulator_19).


To publish data, execute the following command in the FarmSim console:

```
rosPubMsg true
```

If you want to stop publishing data, simply execute:

```
rosPubMsg false
```


#### Subscribing data

Please follow the instructions [here](https://github.com/Helgen-Tech/fs_mod_ros_windows#subscribing-data) to run the ROS node- `cm_vel_subscriber.py` first.



To give control of the manned vehicle to ROS, execute the following command in the FarmSim console:

```
rosControlVehicle true
```

If you want to stop subscribing and gain control back in FarmSim, so you can drive around the farm yourself:

```
rosControlVehicle false
```

#### Optional
Force-center the camera if you use [`d3dshot_screen_grabber`](https://github.com/tud-cor/d3dshot_screen_grabber) to get a simulated RGB camera.

This command is used to fix the camera view while capturing the screen. In FarmSim, the camera view is dynamic when one moves the cursor. In order to simulate the rgb sensor, the camera view should be fixed at one angle as it is mounted on the vehicle.

To force-center the current camera, execute the following command in the FarmSim console:

```
forceCenteredCamera true
```

Stop forcing the camera:

```
forceCenteredCamera false
```

#### ROS Navigation stack
Once you have all the required components, please refer to [fs_mod_ros](https://github.com/Helgen-Tech/fs_mod_ros) to run ROS navigation stack on Linux.
