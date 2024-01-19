[<< Back to Intro Series](intro_series.md)

# Logging a New uORB Topic

Now that we have data publishing out of the module it can be pulled into other modules as well as logged to the SD card for post-flight analysis.

To add loggging support for our `SuperAwesomeTopic` add the topic in `src/modules/logger/logged_topics.cpp`

``` c++
void LoggedTopics::add_default_topics()
{
	. . .
	add_optional_topic("super_awesome_topic", 100); // will log if topic exists at 10 Hz (interval = 100 ms)
	. . .
}
```

Now this topic should show up as long as you have logging enabled and the module running.

:::tip
Note: dont forget to always update the `topic.timestamp` in your module, otherwise it will be hard to analyze the daya since `timestamp` is the x-axis for most log analysis tools.
:::
