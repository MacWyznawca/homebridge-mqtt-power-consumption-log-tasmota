# homebridge-mqtt-power-consumption-log-tasmota

Plugin to HomeBridge optimized for work with Itead Sonoff POW hardware with firmware [Sonoff-Tasmota](https://github.com/arendst/Sonoff-Tasmota) via MQTT with log data to file. Partially emulate Elgato Eve Energy. Measure used power and write logged data to csv files. It acts as a power switch and meter energy consumed.

Like this? Please buy me a beer (or coffee) ;-) <a href="https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&amp;hosted_button_id=CK56Q7SFHEHSW"><img src="http://macwyznawca.pl/donate-paypal2.png" alt="Donate a coder" data-canonical-src="http://macwyznawca.pl/donate-paypal.svg" style="max-width:100%;"></a>

[MacWyznawca.pl](http://macwyznawca.pl) Jaromir Kopp


Installation
--------------------
    sudo npm install -g homebridge-mqtt-power-consumption-log-tasmota

## Description of log files
Period file:
2017-03-06 08:10:00	0.03798	1712	1712	1.00	7.560	213	0.03798	0.700	32.960

**Data and time** \t **period used power [kWh]** \t **power [W] \t ** apparent power [VA] \t power factor \t current [A] \t voltage [V] \t **hourly used power [kWh]** \t **today used power [kWh]** \t **total used power [kWh]**

Hourly file:
2017-03-04 08:00:00	1.23468	9.80263	14.68135	7	6	4

**Data and time** \t **hourly used power [kWh]** \t **today used power [kWh]** \t **total used power [kWh]** \t **hour (UTC)** \t **day of week** \t **day of month**

Daily file:
2017-03-04 08:41:02	9.80263	14.68135	6	4  2

**Data and time** \t **daily used power [kWh]** \t **total used power [kWh]** \t **day of week** \t **day of month** \t **month (0-11)**

Files are tab \t separated.

# Sample HomeBridge Configuration
--------------------

{
	
    "bridge": {
        "name": "Homebridge",
        "username": "CC:22:3D:E3:CE:30",
        "port": 51826,
        "pin": "031-45-154"
    },
	
    "description": "This is an example configuration file. You can use this as a template for creating your own configuration file.",
	
    "platforms": [],
	
	"accessories": [
		{
			"accessory": "mqtt-power-consumption-log-tasmota",
			
			"name": "NAME OF THIS ACCESSORY",
			
			"url": "mqtt://MQTT-ADDRESS",
			"username": "MQTT USER NAME",
			"password": "MQTT PASSWORD",
			
			"topics": {
				"statusGet": "stat/sonoff/POWER",
				"statusSet": "cmnd/sonoff/power",
				"energyGet": "tele/sonoff/ENERGY",
				"stateGet": "tele/sonoff/STATE"
			},
			"onValue": "ON",
			"offValue": "OFF",
			
			"outletInUseBy": "current",
			"outletInUseCurrent": "0.01",
			
			"totalPowerResetBy": "month",
			
	        "activityTopic": "tele/sonoff/LWT",
		    "activityParameter": "Online",
			
			"startCmd": "cmnd/sonoff/TelePeriod",
			"startParameter": "15",
			
			"patchToSave":"/root/.homebridge/",
			"savePeriod": "15",
			
			"timeOffset": "-60",
			
			"manufacturer": "ITEAD",
			"model": "Sonoff TH",
			"serialNumberMAC": "MAC OR SERIAL NUMBER"
		}
	]
}

# Description of the configuration file.


**sonoff** in topic - topics name of Your Sonoff switch.

**"statusGet": "stat/sonoff/POWER"** - status of switch.

**"statusSet": "cmnd/sonoff/power"** - command topic to set switch.

**"energyGet": "tele/sonoff/ENERGY"** - topic for energy telemetry information.

**"stateGet": "tele/sonoff/STATE"** - topic for cyclic telemetry information.

**"onValue": "ON"**, **"offValue": "OFF"** - on and off value for switch. [optional]

**"outletInUseBy": "current"** -  "outlet in use" it will be set only if the circuit current flows. [optional]

**"outletInUseCurrent": "0.01"** - The current above which the "outlet in use" will be activated. [optional]

**"totalPowerResetBy": "month"** - How often reset the consumed power: every "month", "year", or "" - never. [optional]

**"activityTopic": "tele/sonoff/LWT"** - last will topic for check online state. [optional]

**"activityParameter": "Online"** - last will payload for online state. [optional]

**"startCmd": "cmnd/sonoff/TelePeriod"** -  command sent after the connection. [optional]

**"startParameter": "60"** - payload for **startCmd**. [optional]

**"patchToSave":"/root/.homebridge/"** - path to save text files with temperature data. [optional]

**"savePeriod": "15"** - period (minutes) for saving and check temperature. For save only min. and max. 24h temperature data set with "minus" ex "-15". Minimal preriod 10 minutes. Empty: save every 15 min. [optional]

**"zeroHour": "23"** - time (UTC) at which you want to reset the timer min./max. Empty to reset after 24 hours since the last minimum or maximum. [optional]

The files will be saved in the specified path with the "topic" (ex. Sonoff) in the file name ex. "/root/.homebridge/sonoff_period_10.csv", "/root/.homebridge/sonoff_hourly.csv".