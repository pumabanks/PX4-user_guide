# Creating a New Parmeter (yaml)

There are two supported methods for adding a new parameter:

1. Using a `.c` file [shown below](#user-content-using-a-c-file)
    - traditional method
2. Using a `yaml` file [shown below](#user-content-using-a-yaml-file)
    - new convention
    - has helpful extensions


## Using a C File

While `yaml` is the newer supported convention, you can still add parameters using a `super_awesome_module_params.c` file which many modules still use. This method is also the easiest way to add a new parameter, especially if it has very basic properties.

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

:::tip
If the `super_awesome_module_params.c` file is at the top level directory of your module then it will get compiled in by default and is ready to use.
:::

## Using a Yaml File

To add a new parameter using the newer `yaml` convention, create a `module.yaml` file.

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
            category: Developer

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
            category: Developer

```

Then to let the compiler know to add this file into the build process, add the following to the `CMakeLists.txt` file.

``` cmake
	MODULE_CONFIG
		module.yaml
```

So now the `CMakeLists.txt` should look like this:

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
For more information on available yaml parameter settings (i.e. available fields for `unit`, or other parameter options) you can look at the [module_schema.yaml](https://github.com/PX4/PX4-Autopilot/blob/ed0d26de8ab318d10a3e016c01755ae215ea929f/validation/module_schema.yaml#L83C5-L83C5)
:::

:::tip
For a more advanced example of `module.yaml` implementation, check out [mavlink/module.yaml](https://github.com/PX4/PX4-Autopilot/blob/main/src/modules/mavlink/module.yaml)
:::
