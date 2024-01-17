# Creating a New Parmeter (yaml)

There are two supported methods for adding a new parameter:

1. Using a `.c` file [shown here](#user-content-using-a-c-file)
    - traditional method
2. Using a `yaml` file [shown here](#user-content-using-a-yaml-file)
    - new convention
    - has helpful extensions


## Using a C File

While `yaml` is the newer supported convention, you can still add parameters using a `params.c` file which is how many modules still add parameters.

``` c
/**
 * Update rate of the Super Awesome Module
 *
 * @unit Hz
 * @min 0
 * @max 100
 * @reboot_required true
 * @group Super Awesome Module
 *
 */
PARAM_DEFINE_INT32(SAM_EX_UPDATE, 10);
```

If this is at the top level directory of your module then it will get compiled in by default and is ready to use.

## Using a Yaml File

To add a new parameter using the newer convention, create a `module.yaml` file.

``` yaml
module_name: super_awesome_module

parameters:
    - group: Super Awesome Module
      definitions:

        SAM_EX_ENABLE:
          description:
                short: Enable the Super Awesome Module by default
                long: |
                    This will allow the Super Awesome Module to start
                    automatically during system bootup if enabled
            type: enum
            values:
                0: Disabled
                1: Enabled
            reboot_required: true
            default: [1]

        SAM_EX_UPDATE:
            description:
                short: Update rate of the Super Awesome Module
                long: |
                    This will set the rate at which the main Run() gets called.
                    This is the same parameter that is defined in the params.c file
                    and is shown here for comparison
            type: int32
            unit: Hz
            min: 0
            max: 100
            reboot_required: true
            default: [10]

```

Then to let the compiler know to add this file into the build process, we will add the following to our `CMakeLists.txt` file.

``` cmake
	MODULE_CONFIG
		module.yaml
```

So now our `CMakeLists.txt` should look like the following.

``` cmake
px4_add_module(
	MODULE modules__super_awesome_module
	MAIN super_awesome_module
	SRCS
		SuperAwesomeModule.cpp
		SuperAwesomeModule.hpp
	DEPENDS
		px4_work_queue
	MODULE_CONFIG
		module.yaml
)

```

:::tip
For more information on available yaml parameter settings (i.e. available fields for `unit`) you can look at the [module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/validation/module_schema.yaml)
:::
