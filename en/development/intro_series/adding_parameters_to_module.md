[<< Back to Intro Series](intro_series.md)

# Adding Parameters to a Module

In order to allow run-time (or during bootup) configuration changes, PX4 uses **parameters**. We can either add new parameters or pull in existing parameters to our module as needed.

## Making a New Parameter

For creating a new parameter, follow [this tutorial](creating_new_parameter.md)

## Adding Parameter Control to a Module

Now that we have some new parameters lets start by adding inheritance from the `ModuleParams` class in our `SuperAwesomeModule.hpp`

``` c++
// Include the ModuleParams PX4 file
#include <px4_platform_common/module_params.h>

. . .

// Inherit from the ModuleParams base class
class SuperAwesomeModule : public ModuleParams

. . .

private:

	/**
	 * Pull in access to the parameters needed
	 * for this module
	 */
	DEFINE_PARAMETERS(
		(ParamBool<px4::params::SAM_EX_ENABLE>) _param_sam_enable,
		(ParamInt<px4::params::SAM_EX_UPDATE>)  _param_sam_update_hz
	);

```

Now that we have inherited `ModuleParams` and added in parameter access variables, we can access the parameter values by simply calling the `.get()` function as shown below
``` c++
bool enabled = _param_sam_enable.get();

uint32_t update_rate_hz = _param_sam_update_hz.get();
```

## Adding Runtime Support for Parameter Changes

So far we can pull in our parameters but it could be nice to allow the user to change those parameters during run-time. Not all parameters should be monitored for changes during run-time but there are cases where this is helpful.

:::tip
You have to decide on the trade-offs between ***ease of use***, ***performance***, and ***reliability***. For example, it takes resources to monitor parameters so moniitoring at a low and fixed rate using the `SubscriptionInterval` helps. Having many parameters update during `parameters_update()` can cause a hit to performance so choose wisely and decide which trade-off suits your module best.
:::

Add the following to `SuperAwesomeModule.hpp`

``` c++
// uORB parameter_update subscription requirements
#include <uORB/uORB.h>
#include <uORB/SubscriptionInterval.hpp>
#include <uORB/topics/parameter_update.h>

// we will set the subscription interval and this allows us to use 1_s
using namespace time_literals;

. . .

private:

	/**
	 * Process parameter updates
	 */
	void parameters_update(bool force);

. . .

	uORB::SubscriptionInterval _parameter_update_sub{ORB_ID(parameter_update), 1_s};

```

Then add the following to `SuperAwesomeModule.cpp`

``` c++
/**
 * Process parameter updates
 */
void SuperAwesomeModule::parameters_update(bool force)
{
	if (_parameter_update_sub.updated() || force) {

		// clear update flag
		parameter_update_s update{};
		_parameter_update_sub.copy(&update);

		// update parameters from storage
		updateParams();

		// update module parameters
		_enabled = _param_sam_enable.get();
		_update_interval_ms = 1000_ms / _param_sam_update_hz.get();
	}
}
```
