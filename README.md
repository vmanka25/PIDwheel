# PIDwheel
## Table of Contents
* [Planning](#planning)
* [Onshape Design](#onshape-design)
* [Code and Wiring](#code-and-wiring)
* [Construction](#construction)
* [Photos and Videos](#photos-and-videos)
* [Reflection](#reflection)
## Planning
### Objective

To create a box that utalizes PID to control the speed of a motor using and potentiometer. A photointerupter will also be used to calculate the speed of the wheel which will be displayed on an LCD. We will know if our project is succesful if the wheel spins at a speed controlled by PID using a potetentiometer. 

| **Critreria** | **Constraints** |
| ----- | ----- |
| The motor must turn using a potentiometer | Time |
| The LCD must display RPM | Dont use uneseccary materials |
| PID used to adjust speed | |

| **Potential Obstacles** | **Possible Solution** |
| ------ | ------ |
| How to mount the acrylic wheel onto dc motor shaft | make a 3d printed braket connecting the acrylic wheel to the motor shaft |
| We dont know how to use PID | Use google and other resources to learn |
| How to keep the wheel level to prevent collision with the photointerupter | Design multiple wheel prototypes and see which one works best. |

### Milestones

| Date | Milestone | Reflection |
| ------ | ------ | ------ |
| 4/21/23 | Finish Onshape Document | We suceeded in finishing the bulk of the onshape work with relative ease |
| 4/28/23 | Finish Laser cutting, 3d printing and assembling | We suceeded on printing and cutting the parts, we also assembled the acryllic box|
| 5/4/23 | Test motor and other components with our wiring | We had issues with our wheel that delayed the testing |
| 5/15/23 | Implemente code and start testing | We competed the code and got it working but we were behind schedule |
| 5/28/23 | Get project working with PID | We were successful in getting the project working |
| 6/2/23 | Final project done with documentation | Project and documentation was completed by the due date |

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
+ 
## Code and Wiring

### Goal

**Code:** The code is written to first use a potentiometer to control the speed of a dc motor. Then a photointerupter tracks when the each hole in the wheel passes by looking if the state changes. When the state changes the time function is called and logged as time1. When the state changes again a second time is logged and the two are subtracted giving us deltatime. Then using a simple conversion we can calculate RPM. A list was added for fun to add some creativity to the project. Each time an RPM value is calculated it is added to the list. The sum of the list is taken and divided by the length of the list. This calculates average RPM.

### Wiring Diagram

<img src="https://user-images.githubusercontent.com/71350243/236315095-ca4b90ab-fd54-43c9-aba5-c2049de8e78e.png" alt="wiring2" style="width:1000px;">
This is our wiring diagram. The black box with the N is a transistor wich regulates power heading to our Motor and control it. The black tube with the grey line is the diode which serves as a one way switch for the corrent to the motor. The batteries use one switch to turn them on and one is used to power the motor and the other the metro, LCD, photointerupter, and potentiometer. 

### Code Brainstorming

<img src="https://github.com/vmanka25/PIDwheel/blob/main/Media/20230601_214704.jpg?raw=true" alt="wiring2" style="width:318px;">
Code outline I used to help write the code

### Code
This is our code with PID. We got the non PID code to work on the second to last day of class so although we wrote a PID code we never uploaded it to our box and instead used our non PID code. By making this decision we made our box work rather than having a PID box that does't work.
``` python
#Vincent and Zachary 
#5/15/2023
#PID wheel code using a potentiometer

import board
import time
from analogio import AnalogOut, AnalogIn
import simpleio
from lcd.lcd import LCD
from lcd.i2c_pcf8574_interface import I2CPCF8574Interface
from digitalio import DigitalInOut, Direction, Pull
from PID_CPY import PID #Imports PID
from simple_pid import PID

pid = PID(2.0, 0.3, 0.1, setpoint=1) #PID setup
pid.output_limits = (0, 65535)
pid.sample_time = 0.05
pid.tunings = (2.0, 0.3, 0.1)

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
    pid.setpoint = pot.value #target PID is set to the potentiometer value
    motor.value = pid(pot.value) 
    print(simpleio.map_range(pot.value, 96, 65520, 0, 65535)) #maps the motor value to the pot value
    current_photoI = photoI.value #reads photo interupter value
    print(truetime, "Hi")
    print(pot.value, current_photoI, last_photoI)
    time.sleep(.01)
    if(last_photoI == True and current_photoI == False): #Important to remember to use == and not = here.
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
     last_photoI=current_photoI #changes the photoI states
        lasttime=time1 #makes the time1 the new lasttime
        print(lasttime, "hello")
            
            
```
Original Non PID code we used in our videos
``` python
#Vincent and Zachary 5/15/2023
#Wheel code without PID we used on box

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
            lcd.print("RPM: {:.3f}     ".format(current_rpm)) #lcd.print("RPM: {}".format(int(current_rpm)))
            lcd.set_cursor_pos(1, 0)
            lcd.print("AvgRPM: {:.3f}".format(average_rpm))
            time.sleep(.1)
        lasttime=time1 #makes the time1 the new lasttime
        print(lasttime, "hello")
    last_photoI=current_photoI
            
```

### Code and Wiring Reflection

| Code Reflection written by Zach | Wiring Reflection written by Vincent |
| ------ | ------ |
| Writing the code was something I was not looking forward to for this project. Not that I would not be able to, but coding is not always the most interesting to me. Writing this code was far different. It was so much fun and it was the most I had ever learned about a coding language in one project. There were some parts that were rough, but I learned valuable lessons along the way. The first is the usefullness of writing out your program in pseudo code. This helped me organize my thoughts and keep the order of each part straight. Next, I learned how important it is to check your indentations. This code has several embedded if statements and initially, I had things in embedded in if statements that changed the variable to allow me to enter that if statement. This made it impossible to enter the statement and my code was rendered useless. Finally, I learned several minor things, like how code libraries work when implementing the PID and list, and append for calculating average RPM. In conclusion, although we did not have time to fully tweak the PID values the code of this project was very enjoyable and taught me so much. |The wiring wasn’t too hard, just tedious. To wire everything I just combined all of the wirings from my previous projects. I learned a lot too, like how to wire on a mini breadboard. I also learned that you can use a switch to turn off the ground from multiple batteries, which creates one switch that can turn off multiple separate batteries. The hardest part was keeping things orderly but I got it done. |

## Onshape Design

### Sketches

<img src="https://github.com/vmanka25/PIDwheel/assets/71350243/eb5f7142-6a74-4f68-a9e0-c87e311d4c47" alt="wiring2" style="width:400px;">
Brainstorming possible designs for the box

### Goal 

The goal of our onshape design was to create a box that would hold a metro, battery pack, lcd, photinterupter, potentiometer, led and switch. Our inspiration was an old record player so we mounted the wheel on the top of a short box. We first made a box and use the friction fit tool to get the correct amount of offset. We then added out parts into an assembley and by editing in context created the holes and mounts for the battery, led, switch, photointerupter, potentiometer, LCD, and motor. 

### Evidence

[Onshape Link](https://cvilleschools.onshape.com/documents/e3e9160c74c2f05d611e2350/w/8f77f1dc3328ca2505c3c685/e/943fa50f182a6ab5cfa60442?renderMode=0&uiState=64515a94813904144c09a155)

### Onshape Reflection

The design of the box was very easy because we both had extensive experience with onshape and were good at it. We finished the main design of the box multiple days ahead of schedule. The one oversight we had with our design was not making the wheel solid with cut out holes. Initially we added teeth to the outside of the wheel to interupt the photointerupter. This was unfortunate because we did not realize our mistake until after we tested the wheel and saw that it kept cliping the photointerupter. A more thorough review of possible issues with our design would have fixed this problem. It also teaches us the importance of prototypes. If we had considered multiple wheel design at first we would have had alternate solutions that might have been better. Other than that the onshape design went very well. The box was the perfect size and looked very good.

### CAD Images

| Exterior View | Interior View |
| ----- | ------ |
| <img src="https://github.com/vmanka25/PIDwheel/blob/main/Media/Assembly%202.png?raw=true" alt="wiring2" style="width:400px;"> | <img src="https://github.com/vmanka25/PIDwheel/blob/main/Media/Assembly%202%20(1).png?raw=true" alt="wiring2" style="width:400px;"> |


## Construction

We had some issues in the construction of our contraption. The first issue we had is that the divider for our box had tabs that didn't fit so we had to sand down the tabs. Our biggest issue was that our wheel spun with a wobble. We attempted to fix this many times with 3d printing parts and using hotglue. our final solution was a laser cut washer we glued to the wheel to keep it level. we made some other mistakes like designing the wheel with spokes that hit the photointeruptor. we fixed this problem by cutting a wheel with a smooth perimeter. We also made the wheel clear which wouldn't have worked with the photointeruptor. we fixed that by making the wheel black.

## Photos and Videos

| Image of finished project | Video of working box |
| ----- | ----- |
| <img src="https://github.com/vmanka25/PIDwheel/assets/71350243/e19fd326-9d27-43d9-aee7-a17cd31ffafealt" alt="wiring2" style="width:400px;"> | <img src="https://github.com/vmanka25/PIDwheel/assets/71350243/379d1565-033a-4d63-a4d6-24e259c003b0alt" alt="wiring2" style="width:300px;"> |

## Final Project Reflection

Overall this project wasn't too hard and we learned a lot. designing our box and wheel on onshape was by far the easiest part. We made some mistakes with the design of the wheel and some of the fittings on the box but we fixed them. assembling the box was also pretty easy with the exception of our wheel which took many attempts to fix its wobble and other issues. Coding was probably the most difficult part of this project but zach learned alot and wrote code that works. We had one big issue at the end that we beilieve is caused by [motor noise](https://www.pololu.com/docs/0J15/9). this noise is caused by the brushes of the motor bouncing and can causes noise in the wires and can induce noise into other wires. we believe that the noise from our motor caused our LCD to freeze after a couple seconds of the box working. If we had more time a good way to supress motor noise is to solder capacitors across the terminals.
