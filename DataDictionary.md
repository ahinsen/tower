# Data dictionary

The Data dictionary contains data type/structure definitions for database content, and APIs to other levels of the tower (end-devices, gateways, or clients).

Device type hirarchy
--------------------

	Device
	|
	+---End-Device 
	|   |
	|   +---Battery device 
	|   |   |
	|   |   +---MyDeviceType (Application specific device type)
	|   |
 	|   +---Power device 
	|
	+---Gateway 

Battery device 
--------------

config parameters:
- Next wake-up interval
- General wake-up interval
- Wake-up type
- Wake-up pin
- Battery full voltage
- Battery empty voltage
- Channel list
- Channel retry count
- Reply timeout

Sensor reading data:
- Battery voltage


Gateway 
-------
 




must be defined precisely and registered in the server database. Data definitions belong to:
- A scope
	- Global - Data name should be globally (in the tower) unique
	- Device specific - Data name should be unique for a certain device type. Abstract and derived device types form a tree like in the object oriented concept:

End-Device - <list of end-device level data definitions> 
+---Battery device - <list of battery-device specific data definitions>
	+---MyDeviceCategory - <list of category specific data definitions>
		+---MyDeviceType - <list of application specific data definitions>

- A software version (either framework version or application version)

Simple types
------------
	Numeric type code (1 byte)
		1-2 Length (2^n where n=0, 1, 2,or 3) 
		3-4 0-Fixed unsigned / 1-Fixed signed / 2-float(IEE754) / 3-other (16bit:Half 1,5,11, 32bit:Single 1,8,24, 64bit:Double 1,11,52)
		5-8 In case of Fixed: Decimal alignment to be used before being used: x10^d where -8 <= d <= 7
		5-8 In case of Float or other: unspecified
    Array length (1 byte) (0,1: not an array (single), 2-255:array)
	Character encoding (1 byte) optional if numeric type is 0x00
		0:ASCII (7bit)
		1X (hex):UTF-8/16/32 X=0/1/2
		5X(hex): Windows 125X
		9X(hex): 8859-(X+1)	
	List of values: list of  <number 0-255> - <string of  1-10 ASCII characters> pairs. The strings are internal (language independent) short textual representations of the value.
	
	C representation examples of simple types:
	
Complex types
-------------
	List of <Element name(string of  1-10 ASCII characters)> - <simple type> 
	Array length (1 byte) (0,1: not an array (single), 2-255:array)
	
Data definition
---------------
	<Name(string of  1-10 ASCII characters)> - <simple type>
	<Name(string of  1-10 ASCII characters)> - <complex type>
	In order to force simplicity, nested complex types are not preferred (this statement may be revised later)
	

