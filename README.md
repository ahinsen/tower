# Tower
Lightweigh framework for iot projects - under development

This framework supports an 'iot tower' consisting of the following layers:
- End-device (Device)
- Gateway
- Server
- Client

One tower may contain several server instances, each containing a separate physical database, but the content of these databases should be synchronized, or at least partly synchronized. E.g. a company may have two locations with a dedicated local server (and db) for each, and a central server (and db) where both local dbs are synchronized into. This way the central db would have the "full picture" for clients that need it, but locations can safely operate in island mode when necessary. So as a result one tower contains logically one database. End-devices and gateways may connect to several servers, but only as a backup. Primary and backup servers should operate exactly the same way, so logically end-devices and gateways connect to one server.

So the **logical architecture** of a tower is a tree with one server, connecting to several gateways or end-devices, and each gateway connecting to several end-devices, while each end-device and gateway has only one upward connection. The **physical architecture** may look much more complex (many-to-many network topology) but for the rest of this document the logical architecture should be considered.

Devices
-------
An end-device can:
- sense its environment and send measurement readings to the server,
- execute actions (e.g. open a door) by receiving commands from the server.

We group end-devices based on their basic abilities:
- A **Battery Device** generally sleeps most of the time, wakes up on an event (timer, or external hw interrupt) executes a finite series of simple operations (typically read and send sensor data) and goes back to sleep.
- A **Power Device** has both, sufficient power supply, and sufficient processing power for a more more intelligent way of cooperation with the server.

Gateways
--------
Battery devices typically communicate wirelessly. Servers typically communicate over http protocol. Direct wireless communication with servers would require an end-device to use wifi protocol, which is not optimized for power consumption. A battery device should rather use simple radio packet communication to a gateway, and the gateway should communicate with the server using http over either ethernet or wifi. 

Gateways are also power devices in terms of their abilities, but functionally they:
- Act as a server towards the battery devices. 
- Act as an end-device towards the server. More precisely it acts as several end-devices i.e. it represents each battery device that is connected to it.
- Optionally act as an access point for power devices

Logically it doesn't matter for the server if an end-device communicates directly or via a gateway. The route of a message is logged, but only for maintenance purposes.

Server
------
The server contains: 
- A database
- An API to define and maintain a model of the  real world: The objects and attributes to be monitored or controlled by the iot system. (e.g. rooms of a house, or processing batches of a wood drying plant)
- An API to accept object status change commands (e.g. open/close gate) or object attribute manipulating commands (e.g. set target temperature to 25°C)
- an API to communicate with devices: accept sensor readings, update device settings, activate device actions.

The server database contains:
- A registry of all devices and gateways, including the synchronized copy of their configuration parameter settings (values).
- A registry of entities (real world objects) that is necessary for operationg the iot tower. An entity:
  - Has an id and a name.
  - May have references to other entities. E.g. a room as an entity may reference to a building where it is located.
  - May have attributes that are necessary when building complex queries. E.g. the surface of a window may be necessary, if we want to analyze the change in the heating pattern during airing.
  - May have a life-cycle described by a state-machine. E.g. a production batch may be in planned/processing/ready states, and may have the start/finish events triggering the state transitions.
- Several levels of sensed data. The readings from the devices gets processed on different levels:
  - Messages: Messages received from devices are saved in their original form, without parsing or interpreting their content. This dataset also serves as a message log.
  - Sensed values: Messages are interpreted and processed into the sensed values data set. This dataset has one record per sensor per reading
  - Additional data sets for higher level interpretation of sensed data. E.g. a car entering a gate may be just one entry in the "entries" data set, being derived from several 10th of sensed values ( e.g. object arrived licence plate recognized, gate opened, gate closed, ...)
 
This multi-level concept also supports optimal archiving. An entry that is successfully processed for higher levels, can be archived. E.g. messages that are completely and successfully processed into sensed values are not necessary to keep any longer, or similarly gate status changes can be derived from entries, therefore are not needed in their original form. On the other side entries that are not successfully processed should remain to support root cause analysis.

Clients
-------
A client is either a web application with a GUI, or any other application of a larger company interfacing to the iot tower.




