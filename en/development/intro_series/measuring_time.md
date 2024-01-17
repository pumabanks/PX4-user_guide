[<< Back to Intro Series](intro_series.md)

# Measuring Time

PX4 provides a method to measure time by using `hrt_absolute_time()`. This can be used by updating `SuperAwesomeModule.hpp`

``` c++
#include <drivers/drv_hrt.h>

. . .

private:

. . .
	/**
	 * For measuring time since last event
	 */
	hrt_abstime _last_periodic_message{0};

```

Then we could easily send out a periodic message by adding the following to `SuperAwesomeModule.cpp`

``` c++
Run()
. . .

	hrt_abstime now = hrt_absolute_time();

	/**
	 * Send a message every 5 seconds
	 */
	if (now - _last_periodic_message > 5_s) {

		// ex: calculate delta time in micro-seconds (us)
		hrt_abstime dt_us = now - _last_periodic_message;

		// ex: calculate delta time in seconds
		float dt_s = (now - _last_periodic_message) / 1e6;

		// always save timestamp for the last message sent
		_last_periodic_message = now;

		// send out a message
		PX4_INFO("Periodic message (1/2): last call was %llu us ago (%.1f seconds)", dt_us, (double)(dt_s));
		PX4_WARN("Periodic message (2/2): CPU load: %.2f and RAM usage: %.2f", (double)_cpuload.load, (double)_cpuload.ram_usage);
	}

```

:::tip
This also shows how to print a `float` variable that will display with a fixed decimal place precision (1 and 2). In SITL the `PX4_WARN` will show up in the px4 shell or Mavlink console in QGC once you hit enter or interact with it.
:::
