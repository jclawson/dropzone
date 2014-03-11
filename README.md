dropzone
========
Continuous event based configuration framework

Dropzone is a collection of pipes and glue to make it easy to setup events for certain situations, capture relavent attributes for that event, and compare past states to take actions in event listeners.

Goals
-------

* Easily take actions based on changing external state
* Identify changed state values and compare old and new
* Extensible for any kind of monitoring->event->change workflow

Useful Scenarios
--------
Reconfigure your application
* Reconfigure application when a new puppet configuration deployment is synchronized
* Deploy customized script plugin when a script is added to a folder
* Update cache when a new resource package is deployed

Extensible Scenarios
* Poll an external service healthcheck. Close API circuit breaker if it fails.
* Monitor log file
* Monitor linux system changes like a new user was added

General Architecture
--------
```
   SourceMonitor -> ChangeEvent -> StateProcessor -> StateEvent -> StateEventListener
                                ->                              -> ChangeEventListener
```                      
SourceMonitors percieve an external change and generate a change event. From here, registered ChangeEventListeners will recieve the event and may take action based on the raw data. Otherwise, you can introduce a StateProcessor into the system which adds some stateful intelligence. It can compare previous events with current events and generate a StateEvent which will contain information on the changes that occurred. For example, it we had a `FileSourceMonitor` watching `/etc/passwd` it would fire a `ChangeEvent` when a user was added to the system. A `ChangeEventListener` would recieve a copy of the contents of the file. We could also attach a `StateProcessor` which can use the previous known state of `/etc/passwd` to generate a `StateEvent` which would allow direct access to the user information that was added.

Future Plans
---------
* Add built in support for multiple SourceMonitors
* Add built in support for multiple StateProcessors
* Make it easier to build custom StateProcessors
