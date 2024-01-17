# Adding a New Module

For adding a custom application to run on the PX4 stack you will most likely want to add either a new `module` or `driver`.

A `driver` is needed when you are talking to an external periphereal directly (i.e. an airspeed sensor) and a `module` is used when you are adding high level functionality (i.e. a new control algorithm).

:::tip
Adding a new module is helpful because you can create a sandbox to add functionality in.
:::

1. Add new folder in the `modules` folder
  - For this eample we will add `super_awesome_module`
  - It will not do super awesome things yet, but we will add that in later

2. In this folder you will add the following files:
  - [CMakeLists.txt](#user-content-cmakeliststxt)
  - [Kconfig](#user-content-kconfig)
  - [SuperAwesomeModule.hpp](#user-content-superawesomemodulehpp)
  - [SuperAwesomeModule.cpp](#user-content-superawesomemodulecpp)


### CMakeLists.txt

This helps the CMake process and compiler know what needs to be added in and compiled.

```
############################################################################
#   Copyright (c) 2023 PX4 Development Team. All rights reserved.
#
# Terms and license agreement that you must read through before.
# You must copy this big 'ol thing into your file as well as a right-of
# passage and not just for legal reasons :)
#
# NOTE: flying can be dangerous, please code responsibly.
#
############################################################################

px4_add_module(
	MODULE modules__super_awesome_module
	MAIN super_awesome_module
	SRCS
		SuperAwesomeModule.cpp
		SuperAwesomeModule.hpp
	DEPENDS
		px4_work_queue
	)

```

### Kconfig

For now we will set default to `y` to ensure it builds automatically.

```
menuconfig MODULES_SUPER_AWESOME_MODULE
	bool "super_awesome_module"
	default y
	---help---
		Enable support for super awesome functions like hello-world

```

### SuperAwesomeModule.hpp

This is a bare-bones module that has inherited attributes and functions from both the `ModuleBase` and `ScheduledWorkItem` classes.

``` c++
/****************************************************************************
 *
 *   Copyright (c) 2023 PX4 Development Team. All rights reserved.
 *
 * Terms and license agreement that you must read through before.
 * You must copy this big 'ol thing into your file as well as a right-of
 * passage and not just for legal reasons :)
 *
 * NOTE: flying can be dangerous, please code responsibly.
 *
 ****************************************************************************/

/**
 * tell the compiler it only should compile this once
 */
#pragma once

// PX4 includes
#include <px4_platform_common/module.h>
#include <px4_platform_common/px4_work_queue/ScheduledWorkItem.hpp>


namespace super_awesome_module
{
class SuperAwesomeModule : public ModuleBase<SuperAwesomeModule>,
	public px4::ScheduledWorkItem
{
public:
	SuperAwesomeModule();
	~SuperAwesomeModule() override = default;

	/** @see ModuleBase */
	static int task_spawn(int argc, char *argv[]);

	/** @see ModuleBase */
	static int custom_command(int argc, char *argv[]);

	/** @see ModuleBase */
	static int print_usage(const char *reason = nullptr);

	bool init();

private:
	void Run() override;
};

} // namespace super_awesome_module

```

### SuperAwesomeModule.cpp

There is some boilerplate code here that is common to most modules. This is the foundation that we will want to add to in future examples.

``` c++
/****************************************************************************
 *
 *   Copyright (c) 2023 PX4 Development Team. All rights reserved.
 *
 * Terms and license agreement that you must read through before.
 * You must copy this big 'ol thing into your file as well as a right-of
 * passage and not just for legal reasons :)
 *
 * NOTE: flying can be dangerous, please code responsibly.
 *
 ****************************************************************************/


#include "SuperAwesomeModule.hpp"

using namespace time_literals;
using namespace matrix;
namespace super_awesome_module
{

SuperAwesomeModule::SuperAwesomeModule() :
	ScheduledWorkItem(MODULE_NAME, px4::wq_configurations::lp_default)
{
}

bool SuperAwesomeModule::init()
{
	ScheduleOnInterval(100_ms); // 10 Hz
	return true;
}

void SuperAwesomeModule::Run()
{
	if (should_exit()) {
		ScheduleClear();
		exit_and_cleanup();
	}

	/**
	 * This is where we will add our custom functionality
	 * Remember this function should get called at 10Hz
	 * because that is the schedule we set for it
	 */
}

int SuperAwesomeModule::task_spawn(int argc, char *argv[])
{
	SuperAwesomeModule *instance = new SuperAwesomeModule();

	if (instance) {
		_object.store(instance);
		_task_id = task_id_is_work_queue;

		if (instance->init()) {
			return PX4_OK;
		}

	} else {
		PX4_ERR("alloc failed");
	}

	delete instance;
	_object.store(nullptr);
	_task_id = -1;

	return PX4_ERROR;
}

int SuperAwesomeModule::custom_command(int argc, char *argv[])
{
	return print_usage("unknown command");
}

int SuperAwesomeModule::print_usage(const char *reason)
{
	if (reason) {
		PX4_ERR("%s\n", reason);
	}

	PRINT_MODULE_DESCRIPTION(
		R"DESCR_STR(
### Description
Super Awesome Module example.
)DESCR_STR");

	PRINT_MODULE_USAGE_NAME("super_awesome_module", "example");
	PRINT_MODULE_USAGE_COMMAND("start");
	PRINT_MODULE_USAGE_DEFAULT_COMMANDS();
	return 0;
}

extern "C" __EXPORT int super_awesome_module_main(int argc, char *argv[])
{
	return SuperAwesomeModule::main(argc, argv);
}

} // namespace super_awesome_module


```
