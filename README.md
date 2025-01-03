# tower
Lightweigh framework for iot projects - under development

This framework supports an 'iot tower' consisting of the following layers:
- End-device (Device)
- Gateway
- Server
- Client
Devices
-------
An end-device can:
- sense its environment and send measurement readings to the server,
- execute actions (e.g. open a door) by receiving commands from the server
We group end-devices based on their basic abilities:
- A **Simple Battery Device** generally sleeps most of the time, wakes up on an event (timer, or external hw interrupt) executes a finite series of operations (typically read and send sensor data) and goes back to sleep
- A **Listener Device** extends the abilities of a simple battery device with the ability of listening for (waking up on) incoming messages. This ability implies more power consumption and a more intelligent way of cooperation with other elements of the tower.
Gateways
--------

