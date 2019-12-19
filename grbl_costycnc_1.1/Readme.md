https://github.com/grbl/grbl in until grbl 0.9 and https://github.com/gnea/grbl is under grbl 1.0

https://github.com/gnea/grbl/wiki/Grbl-v1.1-Configuration

https://github.com/gnea/grbl/wiki/Grbl-v1.1-Commands  $N

This version is grbl-1.1h ( grbl-1.1h.20190825 )

I used arduino-1.8.10-windows

       1. download https://github.com/costycnc/grbl_costycnc (for original https://github.com/gnea/grbl)
       2. extract zip and open folder 
       3. Arduino Sketch > Include Library > Add .zip library  ... select grbl folder from inside of grbl_costycnc_1.1 folder
       ( for grbl original select grbl folder from inside of grbl-1.1h.20190825 folder)
       4. Now library is installed in Mydocuments/Arduino/libraries you can see grbl folder that contain all files
       5.In Arduino Tools >  i select Board "Arduino nano" and Processor  Atmega328P(old bootloader) and Port
       ( The difference of old and new bootloader is only bauds 57600 vs 115200)
       6.Open Arduino File > Examples > Grbl > GrblUpload ( appear only if library is installed successfully)
       7.Compile and upload
       
First time return error:22  Need to send $N1=F150 https://github.com/gnea/grbl/issues/255       
       
![alt text](https://github.com/gnea/gnea-Media/raw/master/Grbl%20Help/Grbl_Pin_Diagram_v0.9+.png)
![alt text](http://www.costycnc.it/arduino.jpg)

$100,101,102 x,y,z step/mm
$110,111,112 x,y,z max rate mm/min

X_STEP_BIT is now in  cpu_map.h ( old in config.h)


	  // Define step pulse output pins. NOTE: All step bit pins must be on the same port.
	  #define STEP_DDR        DDRD
	  #define STEP_PORT       PORTD
	  #define X_STEP_BIT      2  // Uno Digital Pin 2
	  #define Y_STEP_BIT      3  // Uno Digital Pin 3
	  #define Z_STEP_BIT      4  // Uno Digital Pin 4
	  #define STEP_MASK       ((1<<X_STEP_BIT)|(1<<Y_STEP_BIT)|(1<<Z_STEP_BIT)) // All step bits

	  // Define step direction output pins. NOTE: All direction pins must be on the same port.
	  #define DIRECTION_DDR     DDRD
	  #define DIRECTION_PORT    PORTD
	  #define X_DIRECTION_BIT   5  // Uno Digital Pin 5
	  #define Y_DIRECTION_BIT   6  // Uno Digital Pin 6
	  #define Z_DIRECTION_BIT   7  // Uno Digital Pin 7
	  #define DIRECTION_MASK    ((1<<X_DIRECTION_BIT)|(1<<Y_DIRECTION_BIT)|(1<<Z_DIRECTION_BIT)) // All direction bits

