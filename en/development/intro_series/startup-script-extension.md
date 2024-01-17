[<< Back to Intro Series](intro_series.md)

[<< Back to Starting New Module](intro_series.md)

# Startup Scripts Extension

:::tip
If you have many new modules you could add them to a `rc.custom_apps` file and call that script from `rcS` script.
:::

Add the file to `ROMFS/init.d/CMakeLists.txt`

``` cmake
px4_add_romfs_files(
	. . .
	rc.custom_apps
	. . .
)

```

Add the following to `ROMFS/init.d/rc.custom_apps`

``` bash
#!/bin/sh
#
# Example modules/drivers that should start for all vehicle types
#

#
# start our new module
#
super_awesome_module start

```

Run the above script in the `rcS` startup script


``` bash
#####################
##  rcS            ##
#####################

#
# Lets run our rc.custom_apps script
#
. ${R}etc/init.d/rc.custom_apps

mavlink boot_complete

```