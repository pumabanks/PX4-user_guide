[<< Back to Intro Series](intro_series.md)

# Add CLI Status Feedback

Having command line feedback can really help with debugging and getting a module up and running. Luckily there is already support in the `ModuleBase` class just for this. Let's start by overriding this function in `SuperAwesomeModule.hpp`

``` c++
public:

. . .

	/** @see ModuleBase */
	void print_status() override;
```

Now add in the implementation for the custom status message in `SuperAwesomeModule.cpp` which can be extended in the next tutorials

``` c++
int SuperAwesomeModule::print_status()
{
	uint32_t freq = 1000_ms / _update_interval_ms;

	PX4_INFO("Running at %u Hz", freq);

	return PX4_OK;
}
```
:::tip
In order to invoke this function, simply enter `super_awesome_module status` from the px4 shell (or Mavlink console in QGC)
:::