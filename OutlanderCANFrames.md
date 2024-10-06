I was planning to install a Raspberry Pi behind the stereo in my 2010 ZH Mitsubishi Outlander to play with the interior CANBus , however the car was written off, so I'm not going to be working on this anymore. I've dumped my notes here in case they are of use to anyone else.


## Broadcast

### Hazards

ID: 0x06
Length: 7
Data: xx.00.00.00.00.00.00
Example(s):
    006#03.00.00.00.00.00.00 - flash hazards (on dash at least)

Notes:
xx - seen values 0x03 and 0x1B

 flash the hazards, 1B is used when locking the car,  but replaying doesn't work with the ignition off

### WCM - Wireless Control Module - Door Locks & Immobilizer

ID: 0x012
Length: 6
Data: 
Example(s):
    012#04.02.00.40.00.00 - Unlock doors & disarm alarm
    012#01.02.00.40.00.00 - Lock doors & arm alarm
    012#00.20.00.40.00.00 - Key is in the ignition and switched on, immobilizer chip is detected
Notes: 


### Handbrake

ID: 0x0D0
Length: 2
Data: 
Example(s):
    0D0#84.0 - Park brake on
    0D0#4.0 - Park brake off

Notes:
Only updates when ignition is on



### Door status

Example(s):

Notes:
00000: 13 -- 0|0|0|0|0|0|driver door closed
00000: 13 -- 0|0|1|0|0|0|driver open

00000: 13 -- 0|0|3|0|0|0| - driver & passenger open

00000: 13 -- 0|0|5|0|0|0| - front & rear driver open

00000: 13 -- 0|0|9|0|0|0| - front driver, rear passenger open


00000: 13 -- 0|0|11|0|0|0| - front driver & boot open

00000: 13 -- 0|0|10|0|0|0| - just boot open

### Interior Beeper

Example(s):

Notes:
byte 3 controls duration - 1 long, 2 normal, 3 long (maybe longer than 1?), 4 short
1CB -- 0|0|2|0|0|0|



## Query Response values


### Battery Voltage

Example(s):

Request - can1  6A0   [8]  02 21 AF 00 00 00 00 00
Reply - can1  514   [8]  03 61 AF 7E 01 66 00 00

Notes:

02 and 03 in the first bytes represent how many of the following bytes are important

7E = volts * 10 = 126 = 12.6V
 
 

### Fuel Level

Example(s):
Request - can0  6A0   [8]  02 21 A3 00 00 00 00 00
Reply -  can0  514   [8]  05 61 A3 2C 02 4C 00 00
Notes: 

6th byte - 17 * 0.61 gives an average error of 0.02 L, compared to the app

The actual measurements seem like they might be non-linear, filling up with 47L changed the bytes value by 86, giving a L/Value ratio of 0.5466, but plugging this into the calculations gives values that are ~2-3 high at the low end and ~2-3 low at the high end. 

Finding a better curve might be important when calculating economy, but for now -17 *.61 gives a pretty good match throughout the range.



### Odometer

Example(s):
Request -  can0  6A0   [8]  02 21 AD 00 00 00 00 00
Reply - can0  514   [8]  05 61 AD 77 B6 03 00 00
Notes:

KM in reverse byte order

=G4*65536+F4*256+E4



### Unsorted entries

00000: 2C0 -- 1E|E1|33|19|7F|8|0|40| - car on, brake pedal up
00000: 2C0 -- 1E|E1|33|19|7F|8|0|4C| - car on, brake pedal down
 
00000: 4 -- 58|40|10|1|F0|40| - Driver door unlock (everything else unlocked)
00000: 4 -- 54|40|10|1|F0|40| - Driver door lock (everything else unlocked)

00000: 4 -- 58|40|8|1|F0|40| - drivers door unlock
00000: 4 -- 54|40|8|1|F0|40| - drivers door lock

00000: 4 -- 54|40|9|1|F0|40| }
00000: 4 -- 54|40|8|1|F0|40| } dd already locked, lock others 

00000: 185 -- 2|21|64|2|0|24|21|FF| - 7th byte is AC fan speed
00000: 185 -- 2|21|64|2|0|24|22|FF|   7th byte also has something to do with the recirculation setting
00000: 185 -- 2|21|64|2|0|25|22|FF|   6th byte has to do with the vent selection
00000: 185 -- 2|20|64|2|0|25|22|FF|
00000: 185 -- 2|20|64|2|0|24|22|FF|
00000: 185 -- 2|21|64|2|0|24|22|FF|

