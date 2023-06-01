# PIDwheel
## Table of Contents
* [Planning](#planning)
* [Onshape Design](#onshape-design)
* [Code and Wiring](#code-and-wiring)
* [Photos and Videos](#photos-and-videos)
* [Construction](#construction)
* [Reflection](#reflection)
## Planning
### Sketches

![image](https://github.com/vmanka25/PIDwheel/assets/71350243/eb5f7142-6a74-4f68-a9e0-c87e311d4c47)

### Materials Used

+ Acrylic
+ PLA
+ 1 DC motor
+ 1 Metro M4
+ 1 Photointerrupter
+ 1 Potentiometer
+ LED
+ 1 9v Battery
+ 1 LCD 
+ 1 Switch

### Milestones

| Date | Milestone | Reflection |
| ------ | ------ | ------ |
| 4/21/23 | Finish Onshape Document | We suceeded in finishing the bulk of the onshape work with relative ease |
| 4/28/23 | Finish Laser cutting, 3d printing and assembling | We suceeded on printing and cutting the parts, we also assembled the acryllic box|
| 5/4/23 | Test motor and other components with our wiring | We had issues with our wheel that delayed the testing |
| 5/15/23 | Implemente code and start testing | We competed the code and got it working but we were behind schedule |
| 5/28/23 | Get project working with PID | We were successful in getting the project working |
| 6/2/23 | Final project done with documentation | Project and documentation was completed by the due date |

| **Potential Obstacles** | **Possible Solution** |
| ------ | ------ |
| How to mount the acrylic wheel onto dc motor shaft | make a 3d printed braket connecting the acrylic wheel to the moor shaft |

## Onshape Design
### Goal 
The goal of our onshape design was to create a box that would hold a metro, battery pack, lcd, photinterupter, potentiometer, led and switch. Our insperation was an old record player so we mounted the wheel on the top of a short box. 
### Images
| Exterior View | Interior View |
| ----- | ------ |
| <img src="https://github.com/vmanka25/PIDwheel/blob/main/Media/Assembly%202.png?raw=true" alt="wiring2" style="width:400px;"> | <img src="https://github.com/vmanka25/PIDwheel/blob/main/Media/Assembly%202%20(1).png?raw=true" alt="wiring2" style="width:400px;"> |

### Evidence
[Onshape Link](https://cvilleschools.onshape.com/documents/e3e9160c74c2f05d611e2350/w/8f77f1dc3328ca2505c3c685/e/943fa50f182a6ab5cfa60442?renderMode=0&uiState=64515a94813904144c09a155)
### Reflection




## Code and Wiring
### Goal
### Code
``` python
#Vincent and Zachary 5/15/2023
#Wheel code 

import board
import time
from analogio import AnalogOut, AnalogIn
import simpleio
from lcd.lcd import LCD
from lcd.i2c_pcf8574_interface import I2CPCF8574Interface
from digitalio import DigitalInOut, Direction, Pull
last_photoI = None #lines 11-13 start states for variables
current_photoI = None
lasttime=None
pot = AnalogIn(board.A0) #lines 14-18 setup for components
motor = AnalogOut(board.A1)
photoI= DigitalInOut(board.D7) 
i2c = board.I2C()
lcd = LCD(I2CPCF8574Interface(i2c, 0x27), num_rows=2, num_cols=16)
lcd.print("Motor Starting")
rpm_list=[] #creates a list for all rpm values
truetime=time.monotonic()
while True:
    
    print(simpleio.map_range(pot.value, 96, 65520, 0, 65535)) #maps the motor value to the pot value
    motor.value = int(simpleio.map_range(pot.value, 96, 65520, 0, 65535)) 
    current_photoI = photoI.value #reads photo interupter value
    print(truetime, "Hi")
    print(pot.value, current_photoI, last_photoI)
    time.sleep(.01)
    if(last_photoI == True and current_photoI == False):
        time1=time.monotonic() #reads out time in fractional seconds
        print(time1)
        if(lasttime != None): #checks to see if this is the first state change of the photointerupter
            deltaT=time1-lasttime
            current_rpm=60/(8*deltaT) #calculates rpm
            rpm_list.append(current_rpm) #adds the rpm value to the list
            if(len(rpm_list)>0): #checks if the list length is greater than 0 to avoid dividing by 0
                average_rpm=sum(rpm_list)/len(rpm_list) #calculates average rpm
            else:
                average_rpm=current_rpm
            lcd.set_cursor_pos(0, 0) #lines 36-41 print average and current rpm on the LCD
            lcd.print("RPM: {:.3f}".format(current_rpm)) #lcd.print("RPM: {}".format(int(current_rpm)))
            print(current_rpm)
            lcd.set_cursor_pos(1, 0)
            lcd.print("AvgRPM: {:.3f}".format(average_rpm))
            print(average_rpm)
        last_photoI=current_photoI
        lasttime=time1 #makes the time1 the new lasttime
        print(lasttime, "hello")
            
```
### Wiring Diagram
<img src="https://user-images.githubusercontent.com/71350243/236315095-ca4b90ab-fd54-43c9-aba5-c2049de8e78e.png" alt="wiring2" style="width:318px;">

### Reflection
| Code Reflection written by Zach | Wiring Reflection |
| ------ | ------ |
| I never had enough time to get the code with PID working so our code does not use PID in any way. Despite this I still learned a lot of new thing and have become very comfortable with python. Here are some things I learned while writing this code:

## Photos and Videos

## Construction

We had some issues in the construction of our contraption. The first issue we had is that the divider for our box had tabs that didn't fit so we had to sand down the tabs. Our biggest issue was that our wheel spun with a wobble. We attempted to fix this many times with 3d printing but we designed the 3d printed part with tiny spokes that snapped instantly, we tried hotgluing the laser cut wheel to the 3d printed part but that also failed. we ended up laser cutting a washer that we hotglued and clamped to the wheel. The wheel still wobbles but its good anough and doesn't rub on the potentiometer. 

## Reflection
