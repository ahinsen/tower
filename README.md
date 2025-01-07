# Tower
Lightweigh framework for iot projects - under development

This framework supports an 'iot tower' consisting of the following layers:
- End-device (Device)
- Gateway (Device)
- Server
- Client

One tower may contain several server instances, each having a separate physical database, but the content of these databases should be synchronized, or at least partly synchronized. E.g. a company may have two locations with a dedicated local server (and db) for each, and a central server (and db) where both local dbs are synchronized into. This way the central db would have the "full picture" for clients that need it, but locations can safely operate in island mode when necessary. So as a result one tower contains logically one database. End-devices and gateways may connect to several servers, but only as a backup. Primary and backup servers should operate exactly the same way, so logically end-devices and gateways connect to one server.

So the **logical architecture** of a tower is a tree with one server, connecting to several gateways or end-devices, and each gateway connecting to several end-devices, while each end-device and gateway has only one upward connection. The **physical architecture** may look much more complex (many-to-many network topology) but for the rest of this document the logical architecture should be considered.

Devices
=======

End-devices
-----------
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
======
The server contains a database, and communication API-s towards the devices and different client applications

Database
--------
The database contains the following main sections:
- Device registry: This is a central registry of all devices including both end-devices and gateways. In addition to the basic attributes, each device has a set of configuration parameters. The config parameter set of a certain device depends on the device type. Parameter values can be maintained on the server (see DevOpsAPI) and are synchronized to the individuel devices (see DeviceAPI).
- Device log: Device events are logged locally in the devices, and device logs are synchronized to the server via the DeviceAPI. Device log entries are not measured data, but the information that is needed for the appropriate remote maintenance of the devices (sensor status, sensor reading errors, battery status, etc.).
- Entity registry: A registry of real world objects that are necessary for operationg the iot tower.  Entities describe the technology that is operated using the devices, typically a location hierarchy (e.g.plant/building/room) and technology devices (e.g. machines in a plant, or a window at home) An entity:
  - Has a unique id and a name.
  - May have references to other entities. E.g. a room as an entity may reference to a building where it is located.
  - May have attributes that are necessary when building complex queries. E.g. the surface of a window may be necessary, if we want to analyze the change in the heating pattern during airing.
  - May have a life-cycle described by a state-machine. E.g. a production batch may be in planned/processing/ready states, and may have the start/finish events triggering the state transitions.
- Data monitor: This section consists of several levels of sensed data. The readings from the devices gets processed on different levels:
  - Messages: Messages received from devices are saved in their original form, without parsing or interpreting their content. This dataset also serves as a message log.
  - Sensed values: Messages are interpreted and processed into the sensed values data set. This dataset has one record per sensor per reading
  - Additional data sets for higher level interpretation of sensed data. E.g. a car entering a gate may be just one entry in the "entries" data set, being derived from several 10th of sensed values ( e.g. object arrived licence plate recognized, gate opened, gate closed, ...)
  - This multi-level concept also supports optimal archiving. An entry that is successfully processed for higher levels, can be archived. E.g. messages that are completely and successfully processed into sensed values are not necessary to keep any longer, or similarly gate status changes can be derived from entries, therefore are not needed in their original form. On the other side entries that are not successfully processed should remain to support root cause analysis.
- Data dictionary: The data distionary contains a type hierarchy for devices, entities, and API messages. All data elements of the abovementioned database sections and the API messages are defined in the data dictionary. Some part of the data dictionary belongs to the framework (e.g. basic device types) and is not modifiable by an application. Application specific types and their data elements extend the basic type tree contained by the framwork.
- APIs towards clients:
  -  
- An API to define and maintain a model of the  real world: The objects and attributes to be monitored or controlled by the iot system. (e.g. rooms of a house, or processing batches of a wood drying plant)
- An API to accept object status change commands (e.g. open/close gate) or object attribute manipulating commands (e.g. set target temperature to 25°C)
- an API to communicate with devices: accept sensor readings, update device settings, activate device actions.

 

Clients
-------
A client is either a web application with a GUI, or any other application of a larger company interfacing to the iot tower.




