# Retromod-In-Car-Systems

Notes, collected ideas, and in-progress works related to firmware and software.

Project 240 ECU and beyond
------
This is an in-progress list of sensors and computed values for a do-it-yourself replacement ECU for fuel injected engines. It's still very much in a theoretical/design and viability stage. I return to these notes and brainstorm occasionally, or rearrange values to better split duties, or add more needed values to engine trim, or computational values as I come upon them. 

This is my first ever attempt at any sort of ECU project, but has been born out of an enjoyment of demystifying the Bosch LH 2.x injection used on the venerable Volvo 240. 

You might ask, why?

Curiosity. The desire to one day show up at cars and coffee in a 240 running my own ECU electronics and firmware, to expose an OBDII compatible interface to a visualization app, and to expiriment with positional/performance data logging.

I've done quite a bit with visualization of data from my daily driver Volvo, a V40 1.9t. However, car hacking wasn't something I considered before buying it and I found later that the implementation of CANBUS isn't quite standard and doesn't cover a lot of things I would want to hack around with. 

Someday I'll give up the V40 (my beloved Melvin) and pick up something to CANBUS hack with. But, why not implement my own, something like it? Or, more to my way of learning, connect nodes using something I understand well (like tcp/udp) and then, convert the underlying communication to CANBUS.

All this to say, the LH 2.x replacement ECU is a project with no timeline, and currently slightly more than a thought expiriment. I could very well decide the ECU part of the project is wasted effort and use something off-the-shelf. But, this is one of those things that begs me to ask the dangerous question, "How hard could it be? Really?"

So, now that I've disappointed you thoroughly... Implementing the climate control and cabin/environmental sensor data, them using that to create a modern mostly plug-in replacement climate control system is VERY high on my list of want-tos. I envision this being exposed via a REST api, with a touch enabled contoller. 

Adding in-car infotainment at that stage would be a priority. I picture the touchscreen everything control of the modern high-tech Volvo retrofitted to a 240 series, "Retromod" in a sense not yet seen in the 240 landscape. 

Goals for this doc
------
Think out what the objects exposed might look like? Both OBDII and our own. 

Architecture plan would split the work into multiple controllers (nodes on the network, esp or pi, what have you) -- attempt to assign the source of authority for exposed values to the most sensible places. Establish where duplicates aren't really duplicates (i.e. intake air temp, HVAC/cowl intake temp, external env temperature are all distinct values and have reasons for existing)  

*Objects exposed as a unit (Engine, Cabin, Environment) may represent n sub nodes collected by a master controller node, which would be responsible for exposing the object in one or more ways (CANBUS, api, etc) and allow values to be retrieved or (in appropriate cases) set. 

Engine()
------
````(Basically, all sensors. Dynamic adjustment stuff goes in Engine.somthing)
Intake temp
Intake barometer
MAF/MAP (Ultrasonic?)
Coolant temp
Crank position? (not sure what '83 uses, later uses cps at bellhousing)
O2
Intake air pressure (Vac/boost)
rpm
throttle%
oil pressure
````

Engine().trim()
------
````(some computed values or constants needed I'm aware of. should look at available OBDII fields & Torque)
LTFT1
STFT1
Injector Flow Rate (stored)
Baseline / Static Timing in +/- degrees TDC
Timing Adjust +/- degrees
Effective timing degrees (baseline + adjustment)
````


Engine.meta()
------
````Hours since last oil change
miles since last oil change
hours since t-belt/pump service
miles since t-belt/pump service
miles (this ecu)
miles (base, stored value representing miles at ecu install)
miles (this ecu + stored base mileage variable)
max coolant temp
max rpm
max wheel speed
max vehicle speed (need to either store this or store the val used to compute it at the time)
````
Vehicle()
------
````
angle (yaw) [with dedicated mounted sensor.. dash mounts move too much]
angle (pitch)
G(R-L)
G(Accel/braking)
Brake in
clutch in
parking brake on/off
Voltage
od on/off
gear position?
fuel level
````

Climate/cabin control (damn I want to do this so bad)
------
````
Heater control valve (%)
airbox temp
current cabin temp
target cabin temp
fan speed [0,1,2,3,4](is infinite possible? could set this in percent anyway, implement infinite later?)
defrost servo (in/out)
recirc servo (in/out)
elec defrost switch
Door ajar (and which? 0123)
hatch ajar (4)
Wiper motor (1/2/3 whatever it is)
parking lamps on/off
lights on off
high beam on off
fog lamp switch (front)
fog lamp switch (rear)
signal left
signal right
reverse engaged

(this would all be used by a higher layer GUI controller to expose/control values.
Some significant wiring hacking would be invloved to eliminate the physicial headlight switch or
allow it to be ovverriden in some situations, as well as possible override of windsheild wipers for
auto on w/rain detection, or auto de-icing -- some of this could be augmented with hyper-local weather data
or other sources. photoelec sensor could turn on lights, fog condition detection could turn on foglights (dewpoint/temp I think?)
DRL could be added, with adjustable brightness, blackout on parking brake, etc. 

````

GPS, accel, environment
------
````
ext air temp at cowl
is icing? (intake at cowl below X degrees)
position lat.mins.sec (whatever)
position lon.mins.sec (whatever)
position altitude
photosensor
raining (use whatever.controls.wiper > 0)
wheel speed
vehicle speed (computed, using adjustable diameter/ratio/magic number)
vehicle speed (gps reported)
````

Infotainment Ideas. 
------
````
FM Radio integration. 
Use GPS to update available stations in real-time (Maybe based on FCC maps?)

Expose for infotainment:
Volume level
in car db level
Number of decibels to compensate per mph (this might even be a map)? (use this to actually implement speed sensitive volume)
Mute

````
