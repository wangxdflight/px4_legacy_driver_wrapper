# px4_legacy_driver_wrapper

The Flight Controller AddOn for Snapdragon Flight provides some driver binaries that require wrappers for use with PX4. This repository contains these driver wrappers.

Following these instructions to build the PX4 flight code for Snapdragon Flight using the driver binaries provided by Qualcomm.

## Host Setup

Follow the instructions [here](https://github.com/ATLFlight/ATLFlightDocs) to install the tools, setup the environment variables.
You can optionally build and run [HelloWorld](https://github.com/ATLFlight/ATLFlightDocs/blob/master/HelloWorld.md) to test your setup.

Obtain the Flight Controller AddOn file (qcom_flight_controller_*.zip). The latest version is available from Intrinsyc at http://support.intrinsyc.com/projects/snapdragon-flight/files. Download and extract it to any location on your linux PC. The environment variable FC_ADDON needs to point to the location of the Snapdragon Flight addon.

To use these with PX4, do the following:

```
git clone https://github.com/PX4/Firmware
cd Firmware
git submodule update --init --recursive
cd src/drivers
git clone https://github.com/ATLFlight/px4_legacy_driver_wrapper
cd ../..
export EAGLE_DRIVERS_SRC=`pwd`/src/drivers/px4_legacy_driver_wrapper
export FC_ADDON=<location-of-extracted-flight-controller-addon>
```

## Building the Code
The commands below build the targets for the DSP and the Linux side. Both executables communicate via muORB.
```
cd Firmware
make qurt_eagle_legacy_driver_release
make posix_eagle_legacy_driver_release
```

## Installation on Target
To load the software on the device, make sure the device is booted up, and verify that you can connect to it via USB cable or SSH.

The PX4 software uses configuration files that need to be copied on target. They are located in the following path:
```
Firmware/posix-configs/eagle/<sub-directory>
```

Where <sub-directory> refers to the platform or use-case such as:
- hil: Configuration files for hardware-in-the-loop (HIL) simulation
- flight: Configuration files for a generic flight platform
- 200qx: Configuration files customized for the Microheli 200 MM flight platform

Install the configuration files as follows:
```
cd Firmware
adb push ./posix-configs/eagle/flight/px4-flight.config /usr/share/data/adsp/px4.config
adb push ./posix-configs/eagle/flight/mainapp-flight.config /home/linaro/mainapp.config
```

*NOTE:* The steps above installed the flight config files for a generic flight platform. Please modify the source path and file names based on your platform or use-case.

Install the drivers and binaries as follows:
```
cd Firmware
adb push ./build_posix_eagle_legacy_driver_release/src/firmware/posix/mainapp /home/linaro
adb push ./build_qurt_eagle_legacy_driver_release/src/firmware/qurt/libmainapp.so /usr/share/data/adsp
adb push ./build_qurt_eagle_legacy_driver_release/src/firmware/qurt/libpx4muorb_skel.so /usr/share/data/adsp
adb push ${FC_ADDON}/flight_controller/hexagon/libs/. /usr/share/data/adsp
```

## Execution

Connect to the target via SSH (recommended) or ADB. Then start the PX4 flight software as follows:
```
sudo su
cd /home/linaro
chmod a+x mainapp
./mainapp mainapp.config
```

To stop the flight stack and exit gracefully, run the following commands in the mainapp shell:
```
muorb stop
shutdown
```

## Known Issues and Limitations
Only the following modes / configurations / tools have been tested and therefore supported:
- Flight modes
  - MANUAL (Angle) mode
  - ATLCTL mode
- Quadcopter X airframes
- QGroundControl 2.7.1 (available [here](https://github.com/mavlink/qgroundcontrol/releases/tag/v2.7.1))

The following modes are not functional:
- HITL / HIL mode
## More Information
- Running the mini-dm logging tool: http://dev.px4.io/starting-building.html#run-it
- Setting up auto-start of mainapp upon boot up: http://dev.px4.io/starting-building.html#autostart-mainapp
- Troubleshooting: http://dev.px4.io/advanced-snapdragon.html
- PX4 User Guide: http://px4.io/user-guide
- PX4 Developer Guide: http://dev.px4.io

