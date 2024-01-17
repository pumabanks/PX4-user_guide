[<< Back to Intro Series](intro_series.md)

# Subscribing to a uORB Topic

After getting a module up and running, most likely you will need to pull in data from another `module` or `driver`.

:::tip
Most `modules` at a high-level, simply subscribe to existing data and publish out new data. PX4 uses the **uORB** *pub-sub* model which allows easy exchange of data between `modules` and `drivers`.
:::

To subscribe to topic add the following to the `SuperAwesomeModule.hpp`

``` c++
// add required files for uORB subscription to cpuload topic
#include <uORB/uORB.h>
#include <uORB/Subscription.hpp>
#include <uORB/topics/cpuload.h>

. . .

private:
	// store latest data for the cpuload subscription
	cpuload_s _cpuload{};

	// subscription to a uORB topic
	uORB::Subscription _cpuload_sub{ORB_ID(cpuload)};

```

To monitor this topic add the following to the `SuperAwesomeModule.cpp`

``` c++
Run()
{

. . .

	if (_cpuload_sub.update(&_cpuload)) {

		if (_cpuload.load > 0.85f) {
			PX4_WARN("HIGH CPU LOAD");
		}

		if (_cpuload.ram_usage > 0.85f) {
			PX4_WARN("HIGH RAM USAGE");
		}
	}
}
```

:::tip
Note: this is not a good implementation b/c this will spam the warning message as fast as this module runs but that is intential to create a problem we must solve in subsequent examples by using the hrt_absolute_time() for measuring interval between events.
:::
