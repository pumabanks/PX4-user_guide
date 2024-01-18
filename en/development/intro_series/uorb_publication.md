[<< Back to Intro Series](intro_series.md)

# Publishing a uORB Topic

In order to pass data out of our module we can use the uORB topic we just made. This will require very similar setup to [Subscribing to a uORB Topic](uorb_subscription.md) as shown below in the `SuperAwesomeModule.hpp`

``` c++
// for publishing a uORB topic
#include <uORB/Publication.hpp>

// lets use the new topic
#include <uORB/topics/super_awesome_topic.h>

. . .

private:

	// topic data storage
	super_awesome_topic_s  _topic_out{};

	// handles publishing the data out
	uORB::Publication<super_awesome_topic_s> _topic_pub{ORB_ID(super_awesome_topic)};

. . .

```
:::tip
For better coding practices, especially when you have lots of topics, name your variables based on the topic name (i.e `_super_awesome_topic{}` and `_super_awesome_topic_pub{...}`)
:::

Now in order to publish the data out we must do the following in `SuperAwesomeModule.cpp`

``` c++
. . .
	hrt_abstime now = hrt_absolute_time();

	// update values for topic being published out
	_topic_out.timestamp = now;
	_topic_out.enabled = _enabled;
	_topic_out.update_hz = 1000_ms / _update_interval_ms;
	_topic_out.update_interval_ms = _update_interval_ms;

. . .

	_topic_out.warning_high_cpu_load = cpu_high;

. . .

	// this publishes the latest topic data out
	_topic_pub.publish(_topic_out);

```

:::tip
A nice `CLI` debug tool is to add the `print_message()` that is generated with each uORB topic in our `SuperAwesomeModule::print_status()` function like below
:::

``` c++
int SuperAwesomeModule::print_status()
{
	print_message(ORB_ID(super_awesome_topic), _topic_out);

	return PX4_OK;
}
```
In the PX4 shell this will print out the following:

``` shell
pxh> super_awesome_module status

 super_awesome_topic
    timestamp: 1705616724512000 (0.052000 seconds ago)
    update_interval_ms: 100000
    enabled: True
    update_hz: 10
    warning_high_cpu_load: False

pxh>
```

