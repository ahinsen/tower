# Tower
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
- execute actions (e.g. open a door) by receiving commands from the server.

We group end-devices based on their basic abilities:
- A **Simple Battery Device** generally sleeps most of the time, wakes up on an event (timer, or external hw interrupt) executes a finite series of operations (typically read and send sensor data) and goes back to sleep.
- A **Listener Device** extends the abilities of a simple battery device with the ability of listening for (waking up on) incoming messages. This ability implies more power consumption and a more intelligent way of cooperation with other elements of the tower.

Gateways
--------
Battery devices typically communicate wirelessly. Servers typically communicate over http protocol. Direct wireless communication with servers would require an end-device to use wifi protocol, which is not optimized for power consumption. A battery device should rather use simple radio packet communication to a gateway, and the gateway should communicate with the server using http over either ethernet or wifi. 

Gateways:
- Run on fixed power.
- Act as a server towards the simple battery devices. 
- Act as an end-device towards the server.

Server
------
The server contains: 
- a database
- an API to define and maintain a model of the  real world: the objects and attributes to be monitored or controlled by the iot system. (e.g. rooms of a house, or processing batches of a wood drying plant)
- an API to accept object status change commands (e.g. open/close gate) or object attribute manipulating commands (e.g. set target temperature to 25°C)
- an API to communicate with devices: accept sensor readings, update device settings, activate device actions

Clients
-------
A client is either a web application with a GUI, or any other application of a larger company interfacing to the iot tower.
