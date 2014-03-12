# RGB-LED Lesson 0 – The Absolute Basics GPIO

This lesson is directly based on the supplied material contained within the **User Manual of the RGB-LED Kit** by [**PiHardware.com**](http://pihwardware.com).

If you have the kit and have already completed the manual, then you may wish to skip to the bottom and try the “extra credit” exercises and move onto Lesson 1.

![alt text](http://i748.photobucket.com/albums/xx122/meltwaterhtc/RGB-Colours_zpsc2f7dec0.jpg "Driving the RGB-LED Kits from the Raspberry Pi GPIO – Click on Image for details on the Kit")


Driving the RGB-LED Kits from the Raspberry Pi GPIO – [For details on the Kit](http://pihw.wordpress.com/category/hardware-kits/rgb-led-kit/).


## Basic Programming:
Within these instructions, we shall only cover some very basic programming, more detailed examples and tutorials will be made available from the **Meltwater Raspberry Pi Hardware** site [http://pihwardware.com](http://pihwardware.com) in the learning section.

It is highly recommended that you take the time to type coding examples in yourself (even if the source is available).  By doing so you will become familiar with the syntax and format of the commands and you will learn it far quicker than if you cut and paste it.

### Setup:

#### Hardware Connections:

Unless specified otherwise, the guides and tutorials will assume you have your board wired up as follows (although we should always aim to make it easy in our programs to adapt the wiring if required).

| **RGB LED**     |     |     |     |     | 01  |     | 02  | 03  |     | 04  |     |     |
|:-------------:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **RPi GPIO Pin**  | 2 | 4 | 6 | 8 | 10 | 12 | 14 | 16 | 18 | 20 | 22 | 24 | 26 |
| **Rpi GPIO Pin**  | 1 | 3 | 5 | 7 | 9  | 11 | 13 | 15 | 17 | 19 | 21 | 23 | 25 |
| **RGB LED**     |     |     |     |**05**|     |**R**|**G**|**B**|     |     |     |     |

See below for the correct orientation of the GPIO Port (P1).

![alt text](http://pihw.files.wordpress.com/2013/02/rpigpiop1.jpg?w=614 "Raspberry Pi GPIO Port Pins")

#### SD Card Image:
The guides will assume a clean, up to date system image.  To obtain the latest Raspbian SD Card image, and for details on how to write it to your SD Card see the Raspberry Pi Homepage download section [http://www.raspberrypi.org/downloads](http://www.raspberrypi.org/downloads).


###Using Bash (controlling LEDs using basic scripting):
First we will write a short program script using Bash.  Bash is a common shell used with Linux, it allows you to write basic commands, and importantly for us to write and run scripts (a long list of commands).

The example here only shows controlling one LED but hopefully if you look at it carefully you can see how to extend it to control more than one (and perhaps how to produce different colours).

Create the file by typing the following from the terminal (command prompt) on your Raspberry Pi.
```
    nano rgbledtest.sh
```

Type in the following script and then save and exit (**Ctrl+x,y and enter**).

Run the script:
```
    sudo bash rgbledtest.sh
```
If you get any errors, then use nano again and examine your script for errors.

#####Script: rgbledtest.sh
```shell
#!/bin/sh
# Bash uses BCM GPIO numbers (i.e. the pin names of the Broadcom Chip itself)
# These are detailed in the Raspberry Pi wiki pages.
# Therefore to make life easier we will map them to the Raspberry Pi GPIO
# Pin Numbers.
PIN12=18
PIN16=23
PIN18=24
PIN22=25
PIN7=4
PIN11=17
PIN13_REV1=21
PIN13_REV2=27
PIN15=22
 
#Setup Pins
LED01=$PIN12
LED02=$PIN16
LED03=$PIN18
LED04=$PIN22
LED05=$PIN7
LED_RED=$PIN11
LED_GREEN=$PIN13_REV2 #Rev2.0 (Ensure you use correct one for your board)
LED_BLUE=$PIN15
 
#Setup Active states
#Common Cathode RGB-LEDs (Cathode=Active Low)
LED_ENABLE=0
LED_DISABLE=1
RGB_ENABLE=1
RGB_DISABLE=0
 
#Set up GPIO to Outputs
# Set up GPIO Pin, to output and put in disable state
echo "$LED01" > /sys/class/gpio/export
echo "$LED_RED" > /sys/class/gpio/export
echo "$LED_GREEN" > /sys/class/gpio/export
echo "$LED_BLUE" > /sys/class/gpio/export
echo "out" > /sys/class/gpio/gpio$LED01/direction
echo "out" > /sys/class/gpio/gpio$LED_RED/direction
echo "out" > /sys/class/gpio/gpio$LED_GREEN/direction
echo "out" > /sys/class/gpio/gpio$LED_BLUE/direction
echo "$LED_DISABLE" > /sys/class/gpio/gpio$LED01/value
echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_RED/value
echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_GREEN/value
echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_BLUE/value
# Setup End
############################################################
 
#Switch on LED with RED/GREEN/BLUE
echo "$RGB_ENABLE" > /sys/class/gpio/gpio$LED_RED/value
echo "$RGB_ENABLE" > /sys/class/gpio/gpio$LED_GREEN/value
echo "$RGB_ENABLE" > /sys/class/gpio/gpio$LED_BLUE/value
echo "$LED_ENABLE" > /sys/class/gpio/gpio$LED01/value
#Wait
sleep 1s
#Switch each LED off
echo "$LED_DISABLE" > /sys/class/gpio/gpio$LED01/value
echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_RED/value
echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_GREEN/value
echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_BLUE/value
 
############################################################
# Clean Up
echo "$LED01" > /sys/class/gpio/unexport
echo "$LED_RED" > /sys/class/gpio/unexport
echo "$LED_GREEN" > /sys/class/gpio/unexport
echo "$LED_BLUE" > /sys/class/gpio/unexport
#End
```


#####Script Details:
By using the = sign we are able to assign values to “variables”, this gives us an easy way to make our script easy to modify (for instance if we change the wiring).  To reference a “variable” the $ sign is used, this tells Bash to look for a variable and put whatever it is value is in its place.

For instance:
```shell
PIN12=18
LED01=$PIN12
```

Is the same as:
```shell
LED01=18
```

The script includes the mapping for all the pins used by the RGB LED board, so you can continue to set-up the rest of the pins and try the other LEDs.

To use a GPIO pin in Bash, you have to do the following:

1. Export the pin number:
  ```
  echo "$LED01" > /sys/class/gpio/export
  ```
2. Set the direction of the pin (input = “in” or output = “out”):
  ```
  echo "out" > /sys/class/gpio/gpio$LED01/direction
  ```
3. Initialise it to a safe/default state:
  ```
  echo "$LED_DISABLE" > /sys/class/gpio/gpio$LED01/value
  ```
4. Use it, by setting it to a value 0 or 1:
  ```
  echo "$LED_ENABLE" > /sys/class/gpio/gpio$LED01/value
  ```
5. Return it to a safe/default state (not required but good practice):
  ```
  echo "$LED_DISABLE" > /sys/class/gpio/gpio$LED01/value
  ```
6. Release the pin when you are done with it:
  ```
  echo "$LED01" > /sys/class/gpio/unexport
  ```

Finally, we use sleep to provide a 1s (1 second) delay between actions.

**That covers the very basics!  Next we will look at how to do something similar using Python.**



###Using Python (controlling LEDs using RPi.GPIO):
Before you start you will need to ensure you have the latest RPi.GPIO library installed.

To do this, connect your Raspberry Pi to the internet and run the following commands from the terminal:

sudo apt-get update
sudo apt-get install python-dev python-rpi.gpio
As for the bash example above, create the file by typing the following from the terminal (command prompt) on your Raspberry Pi.

nano rgbledtest.py
Type in the following script and then save and exit (Ctrl+x, y and enter).

Run the script:

sudo python rgbledtest.py
If you get any errors, then use nano again and examine your script for errors.

####Script: rgbledtest.py

```python
#!/usr/bin/python
import time
import RPi.GPIO as GPIO
 
# RGB LED Module (TEST)
 
#Setup Active states
#Common Cathode RGB-LEDs (Cathode=Active Low)
LED_ENABLE = 0
LED_DISABLE = 1
RGB_ENABLE = 1
RGB_DISABLE = 0
 
#LED CONFIG - Set GPIO Ports
LED1 = 12   #B4
LED2 = 16   #B18
LED3 = 18   #B23
LED4 = 22   #B24
LED5 = 7    #B25
LED = [LED1,LED2,LED3,LED4,LED5]
RGB_RED = 11   #B22
RGB_GREEN = 13 #B21 Rev1  B27 Rev2
RGB_BLUE = 15  #B17
RGB = [RGB_RED,RGB_GREEN,RGB_BLUE]
 
def led_setup():
  #Set up the wiring
  GPIO.setmode(GPIO.BOARD)
  # Setup Ports
  for val in LED:
    GPIO.setup(val, GPIO.OUT)
  for val in RGB:
    GPIO.setup(val, GPIO.OUT)
 
def led_activate(led,colour):
  GPIO.output(led,LED_ENABLE)
  GPIO.output(colour,RGB_ENABLE)
 
def led_deactivate(led,colour):
  GPIO.output(led,LED_DISABLE)
  GPIO.output(colour,RGB_DISABLE)
 
def led_clear():
  for val in LED:
    GPIO.output(val, LED_DISABLE)
  for val in RGB:
    GPIO.output(val, RGB_DISABLE)
 
def main():
  led_setup()
  led_clear()
  led_activate(LED1,RGB_RED)
  time.sleep(5)
  led_deactivate(LED1,RGB_RED)
  led_clear()
  GPIO.cleanup()
 
main()
#End
```

####Script Details:
At the top of this file we “import” 2 libraries:

1. **time** – which allows us to use the time.sleep() command (just like the bash script this gives us an easy way to put in delays – the value is in seconds, 0.5 would be a ½ second, 0.001 is 1ms)
2. **RPi.GPIO** – the installed library allows python to access the GPIO pins (to use this we need to ensure we use sudo)
Next we define our pins and signals, so that as before we can quickly change things around if needed without changing in multiple places.

Now, we define 5 basic functions:

1. **led_setup():** This sets the pin reference  and each of the pins to outputs.
2. **led_activate(led, colour):** Basic function to switch on a specific LED and activate the specified colour.
3. **led_deactivate(led, colour):** Function to switch the LED off again.
4. **led_clear():** Function to return the LEDs to a known deactivated state.
5. **main():** This function called on start, makes calls to each of the above to setup, clear and then activate the required LED for a set period of time.  It then uses GPIO.cleanup() to release the pins before exiting.

##Extra Credit:
The following exercises can be performed using both Bash and Python.

To create a loop using Bash, the following code can be used:

```shell
for i in 1 2 3 4 5 6 7 8 9 10
  do
  #write output
  echo "$RGB_ENABLE" > /sys/class/gpio/gpio$LED_RED/value
  echo "$RGB_DISABLE" > /sys/class/gpio/gpio$LED_RED/value
  done
```
To create a loop using Python, the following code can be used:

```python
for i in range(10):
  led_activate(LED1,RGB_RED)
  time.sleep(5)
  led_deactivate(LED1,RGB_RED)
  time.sleep(1)
```

1. Try extending each of the examples to cycle through the colours and each LED in turn.
2. Try using a combination of RGB to create more colours.


###[RGB-LED Lesson 0 – Extra Credit Solution](http://pihw.wordpress.com/lessons/lesson-0-extra-credit-solutions/)

###[Next RGB-LED Lesson 1 – Creating Python Libraries (& Colours)](http://pihw.wordpress.com/lessons/rgb-led-lesson-1-creating-python-libraries-colours/)
