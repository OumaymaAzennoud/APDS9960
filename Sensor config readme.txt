-----------------------------------------------------------------------------------------------------------
Raspberry pi (We used Rpi 3 Model B+)
-----------------------------------------------------------------------------------------------------------

◘ Downoald RPi Imager
◘ Connect the SD card to the PC
◘ Rpi Imager
	- Operating System : RPi OS (other) → Rpi OS Lite (32-bit)
	- SD card : Select your SD Card 
	- Write → Yes (This will take a few minutes)

◘ Insert the SD card in the RPi
◘ Connect an HDMI cable, a mouse and a keyboard to the Rpi
◘ Open a terminal in the RPi desktop
	sudo su -
	raspi-config
	Goto 3.Interface → activate I2C and SSH
	

-----------------------------------------------------------------------------------------------------------
Putty
-----------------------------------------------------------------------------------------------------------
◘ Open Putty
◘ Select SSH and enter the Rpi IP adresse (10.151.51.107 Port22)
	I found the adress with Advanced IP Scanner app : Manifacturer=RasberryPi, if you disconnect the RPi one of the adresses goes Grey
◘ Open the Putty session, enter username and password.

____
Note
____
We can access the Table to enable I2C via putty:
	Login with username & password
	sudo raspi-config
	Goto interface and enable I2C..



-----------------------------------------------------------------------------------------------------------
Sparkfun APDS9960 Sensor
-----------------------------------------------------------------------------------------------------------
	
◘ Connections
___________
(See picture Rpi 3 Model B+ pinout)

	Sensor	|  RPi
	________|_____________
	GND	|  GND-pin9
	VCC	|  3V3-pin1
	SDA	|  SDA-pin3
	SCL	|  SCL-pin5
	INT	|  GPIO17-pin11



◘ Check if the Sensor is being detected
	in the putty terminal after login enter:
	sudo i2cdetect -y 1

	The first column represents the I2C bus addresses in hex format, The second column shows the devices detected at each address.
	Address 39 is detected, which indicates that there is a device present at that address on the I2C bus.
	If no Adress is detected, reconnect the sensor.

     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:                         -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- 39 -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- -- -- -- -- -- -- --

	


-----------------------------------------------------------------------------------------------------------
Instal Packages and libraries
-----------------------------------------------------------------------------------------------------------


◘ First, create a new environement
	python -m venv myenv
	source myenv/bin/activate
	sudo pip install apds9960
	###############################
	This solved the gesture problem
	###############################
	pip3 install adafruit-circuitpython-apds9960  

◘ Now you create your directory
	mkdir work
	cd work/
	mkdir light_sensor
	cd light_sensor/
	
◘ Now you create your python scripts
	nano filename.py // This create or opens a file if it already exists
	the files is opened and you can put your code
	- To save : ctr+o → Enter 
	- To exit the script : ctr+x
	- To stop the runing loop : ctr+c

	




-----------------------------------------------------------------------------------------------------------
Issues Fix
-----------------------------------------------------------------------------------------------------------
If you are buying the sensor from eBay. Some of these boards are clones with cheaper components.
The issue with these boards is that you can’t be sure if they contain a genuine APDS9960 or just a dummy chip that looks like the original.

In this case, you have to modify the library’s SparkFun_APDS9960.cpp file. (In my case it was a file in myenv in the Rpi : myenv/lib64/python3.11/site-packages/apds9960 we need to modify Device.py and const.py)


in SparkFun_APDS9960.cpp or in my case Device.py:
-------------------------------------------------
Change the line that sets the LED boost to read:if (!setLEDBoost(LED_BOOST_300)) becomes (!setLEDBoost(LED_BOOST_100))

in const.py
-----------
APDS9960_DEFAULT_LDRIVE = APDS9960_LED_DRIVE_100MA change to APDS9960_DEFAULT_LDRIVE = APDS9960_LED_DRIVE_50MA

By setting APDS9960_DEFAULT_LDRIVE to 50 mA, you’re ensuring that the IR LED emits more intense light, which can increase the sensor’s sensitivity.

















