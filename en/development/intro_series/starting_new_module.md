[<< Back to Intro Series](intro_series.md)

# Starting a Module

## Starting Module from Shell

Now that your module is ready to compile in with the PX4 stack, you must build it. Below are two examples for building the PX4 code:


``` bash
# build and run locally in a simulated environment
make px4_sitl jmavsim

# (or) build and run this to upload to an autopilot (ex: v6x)
make px4_fmu-v6x_default upload
```

Once the code is running locally or loaded onto your autopilot open QGroundControl and test out our module.

:::tip
If running locally in SITL, you can simply hit enter in the terminal where you started the simulation, and then you will be in the PX4 shell. All the below commands can be typed in there as well. QGroundControl will work for either SITL or real autopilot
:::

In QGroundControl navigate to Analyze Tools (Q button in top left) then Mavlink Console. From there, test the new module:


``` bash
# to check our module's current status
super_awesome_module status

# to see the list of cli arguments and options
super_awesome_module

# to start are module
super_awesome_module start
```

:::tip
After starting the module you should check the status and confirm it is running.
:::


## Starting Module by Default

So starting our new module in the shell is fun but not always practical. If this module should always start by default during bootup we must add the startup command for this module somewhere in the startup sequence scripts for PX4.

``` bash
#
# for SITL:      ROMFS/px4fmu_common/init.d-posix/rcS
# for autopilot: ROMFS/px4fmu_common/init.d/rcS
#
# for both add the follwing to the bottom just before the mavlink boot_complete call
#

 . . .

#
# start our new module
#
super_awesome_module start

mavlink boot_complete

```

:::tip
Deeper integration could be done by only starting the module for certain vehicle types (i.e. for a plane-only module add the start command to the `ROMFS/px4fmu_common/init.d/rc.fw_apps` script).
:::

## Additional Example

You can find [this example](startup-script-extension.md) for further customization of the startup script process

