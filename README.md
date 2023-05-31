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
### Materials Used

+ Acrylic
+ PLA
+ DC motor
+ Arduino
+ Photointerrupter
+ Potentiometer
+ LED
+ 9v Battery
+ LCD Backpack
+ Switch

### Milestones

| Date | Milestone | Reflection |
| ------ | ------ | ------ |
| 4/21/23 | Finish Onshape Document | We suceeded in finishing the bulk of the onshape work with relative ease |
| 4/28/23 | Finish Laser cutting, 3d printing and assembling | We suceeded on printing and cutting the parts, we also assembled the acryllic box|
| 5/4/23 | Test motor and other components with our wiring | We had issues with our wheel that delayed the testing |
| 5/15/23 | Implemente code and start testing | We competed the code and got it working but we were behind schedule |
| 5/28/23 | Get project working with PID | PID was never fully and correctly incorperated |
| 6/2/23 | Final project done with documentation | Project and documentation was completed by the due date |


## Onshape Design
### Goal
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
#Wheel code no PID

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

## Photos and Videos

## Construction

## Reflection
