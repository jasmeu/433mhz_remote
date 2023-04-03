# 433 Mhz Remote Control - Home Automation

## Intro
TBD - How to setup the Raspberry Pi to run Python apps.

## Use Case - 3T Motors

The use case described here refers to controlling blinds motors from `3T-Motors` controlled by the `3T Mini-Funk-Empfänger MFE` (3T Motors Mini Receiver). Unfortunately, there is little info on the web how to control these motors with a Raspberry Pi, so I had to do a little bit of work myself, and this work I'd like to share with you. The info here is probably applicable also for other devices, but also there: Be ready for a bit of work :).  
First of all: For my case, such [tutorials](https://www.instructables.com/RF-433-MHZ-Raspberry-Pi/) do not unfortunately work. So don't worry if they do not work for you either; Although the receiver module might perfectly work (as hardware), the receive.py code will not display anything, probably because the received protocol is unknown.   
And this is the case where a tutorial like [Super Simple Raspberry Pi 433MHz Home Automation](https://www.instructables.com/Super-Simple-Raspberry-Pi-433MHz-Home-Automation/) from [george7378](https://www.instructables.com/member/george7378/) will save you / me.  

## Hardware Setup 
There's nothing to add to the mentioned tutorial: [Super Simple Raspberry Pi 433MHz Home Automation](https://www.instructables.com/Super-Simple-Raspberry-Pi-433MHz-Home-Automation/), except for: as it was not clear in the referenced tutorial [Super Simple Raspberry Pi 433MHz Home Automation](https://www.instructables.com/Super-Simple-Raspberry-Pi-433MHz-Home-Automation/), I connected the modules like in [this tutorial](https://www.instructables.com/RF-433-MHZ-Raspberry-Pi/).

As such - taking into account that GPIO is set on GPIO.BCM mode - using the pinouts map from [here](https://pinout.xyz/), I found out that my receiver is on pin `27` and transmitter on `17`.

## Software
Although I got the OK from the author of [Super Simple Raspberry Pi 433MHz Home Automation](https://www.instructables.com/Super-Simple-Raspberry-Pi-433MHz-Home-Automation/) to extend his code / publish the updated version, I don't know exactly which are the rights around the code posted on [www.instructables.com](https://www.instructables.com) so I'll refrain posting here the entire updated code; Instead I'll show you the delta.  

There are 2 important changes I had to do:
- I have no computer monitor around, so I work with my Raspberry Pi as headless system with SSH from my Mac. Hence: no chance to display and chart like in the receiver code.
- For this project I used a Raspberry Pi 2.0 which not fast enough to record the received values in very narrow steps and thus the time estimation around how long the `short_delay` and `long_delay` should be in the sender is extremely inaccurate.

### Receiver

For the receiver, only 1 second recording the values is enough. Thus: `MAX_DURATION = 1`.    
I commented out all the print statements and all the code around pyplot.   
I added a `print('Time,Bit')` before the for-loop and inside the loop a `print(str(RECEIVED_SIGNAL[0][i]) + ',' + str(RECEIVED_SIGNAL[1][i]))` after calculating the value of `RECEIVED_SIGNAL[0][i]`.   
I start the program with `python3 ReceiveRF.py > a.csv` and right after the program starts I make sure to push the appropriate button on the remote and keep pushed till the program finishes (1 second later).  
I extract the `a.csv` file from the Pi via: `scp <pi_user>@<pi_hostname>:/home/<pi_user>/a.csv ./a.csv`
In order to visualize the CSV file, I use the wonderful online chart creator from [datawrapper.de](https://app.datawrapper.de/).  
I try to identify in the chart 1-2 blocks of signal without errors, and I write down the associated code, as explained in the referenced tutorial. For example, I identified a code like `1110001110101100111100001011000011`.  
Hints:  
- validate with several signal blocks if you got the right code
- repeat the process and record once more the same button on the remote. Make sure it's the same code. If so, perfect: continue. Else, sorry for you, I have no idea how to proceed, as you might have a system with rolling code.  

### Transmitter
As we hopefully somehow got the right code, we must find out another very important piece of info: How long is the "long signal" and how long is the "short signal".
