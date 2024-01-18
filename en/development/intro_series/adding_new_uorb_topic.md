

# Add a New uORB Topic

Now in order to pass data or commands out of our class we may find it helpful to make a new topic for our module to use.

:::tip
All uORB topics are located under the [`/msg`](https://github.com/PX4/PX4-Autopilot/tree/main/msg) folder so you can see what all topics are available.
:::

We start by adding a `SuperAwesomeTopic.msg` file like below in the `/msg` folder

``` yaml
uint64 timestamp                 # time since system start (microseconds)

bool enabled                     # true (1) if the SuperAwesomeModule enabled
uint8 update_hz                  # configured update rate in Hz
uint8 update_interval_ms         # configured update interval in ms

float32 HIGH_CPU_LOAD_WARNING = 0.85       # threshold for high cpu load warning

uint32 WARN_MESSAGE_INTERVAL_US = 5000000   # warning message interval sent at this interval (in micro seconds)

bool warning_high_cpu_load       # actively warning user about cpu usage

```

Now we will need to make sure we generate that topic when we build the code by adding the message to `msg/CMakeLists.txt`

``` cmake

set(msg_files
	ActionRequest.msg
	. . .
	SuperAwesomeTopic.msg
	. . .
)
list(SORT msg_files)

```

:::tip
Note: during uORB code generation the topic name gets converted from `camelCase` to `snake_case` for actual c++ generated code. For example `SuperAwesomeTopic.msg` gets converted to the `super_awesome_topic.cpp` file. In this file you'll find the generated c++ stuct that represents the message and its name appends a `_s` at the end (i.e. `super_awesome_topic_s`).
:::

## Using Topic Constants

You may have noticed that we added two constants to the message, `HIGH_CPU_LOAD_WARNING` and `WARN_MESSAGE_INTERVAL_US`. These can come in handy if multiple modules need to know some common constant values. See below for an example of using these.

``` c++
// remember to put this in you SuperAwesomeModule.hpp file
#include <uORB/topics/super_awesome_topic.h>

// in the Run() of our SuperAwesomeModule.cpp file
. . .

		// check threshold
		bool cpu_high = _cpuload.load > super_awesome_topic_s::HIGH_CPU_LOAD_WARNING;

		// check for time elapsed between messages
		bool time_for_message = now - _last_warning_message > super_awesome_topic_s::WARN_MESSAGE_INTERVAL_US;

		if (cpu_high && time_for_message) {

			_last_warning_message = now;
			PX4_WARN("HIGH CPU LOAD");
		}

. . .

```