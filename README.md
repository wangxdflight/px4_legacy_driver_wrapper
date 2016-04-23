# px4_legacy_driver_wrapper

The FC addon for Snapdragon flight contains binary drivers that require wrappers for use with PX4.
This repository contains the driver wrappers.

Make sure the required env variables are set after installing the Hexagon SDK and gcc cross compiler.

To use these with PX4 you need to do the following:

```
git clone https://github.com/PX4/Firmware

cd Firmware/src/drivers
git clone https://github.com/ATLFlight/px4_legacy_driver_wrapper
cd ../..
export EAGLE_DRIVERS_SRC=`pwd`/src/drivers/px4_legacy_driver_wrapper
make qurt_eagle_legacy_driver_release
make posix_eagle_legacy_driver_release
```
