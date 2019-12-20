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
       
![alt text](https://github.com/costycnc/grbl_costycnc/blob/master/grbl_costycnc_1.1/v0.9%2B.png)
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

----

	  
	  C:\Users\costycnc\Downloads\grbl_costycnc-master\grbl_costycnc-master\grbl_costycnc_1.1\grbl_costycnc_1.1\cpu_map.h (2 hits)
		Line 39: 	  #define X_STEP_BIT      2  // Uno Digital Pin 2
		Line 42: 	  #define STEP_MASK       ((1<<X_STEP_BIT)|(1<<Y_STEP_BIT)|(1<<Z_STEP_BIT)) // All step bits
	  C:\Users\costycnc\Downloads\grbl_costycnc-master\grbl_costycnc-master\grbl_costycnc_1.1\grbl_costycnc_1.1\settings.c (1 hit)
		Line 319:   if ( axis_idx == X_AXIS ) { return((1<<X_STEP_BIT)); }
	  C:\Users\costycnc\Downloads\grbl_costycnc-master\grbl_costycnc-master\grbl_costycnc_1.1\grbl_costycnc_1.1\stepper.c (1 hit)
		Line 421:     st.step_outbits |= (1<<X_STEP_BIT);
----
	  ISR(TIMER0_COMPA_vect)
	  {
		STEP_PORT = st.step_bits; // Begin step pulse.
		#ifdef ENABLE_DUAL_AXIS
		  STEP_PORT_DUAL = st.step_bits_dual;
		#endif
	  }
	#endif
	
line 427 stepper.c

              if (st.exec_block->direction_bits & (1<<X_DIRECTION_BIT)) { sys_position[X_AXIS]--; }
              else { sys_position[X_AXIS]++; }
	      
Tutorial:

		 #define X_STEP_BIT      2 
		 int buttonState = B0;
		 buttonState |= (1<<X_STEP_BIT);
		 Serial.println(buttonState,BIN);
		 Result > 100     (4)
	      
	      PORTD = PORTD & B00000011;  // clear out bits 2 - 7, leave pins PD0 and PD1 untouched (xx & 11 == xx)
	      DDRD = DDRD | B11111100;    // set out bits 2 - 7, leave pins PD0 and PD1 untouched (xx | 00 == xx)
	      
Begin modify 20.12.2019	      

	Search "STEP_PORT" (9 hits in 2 files)
	  C:\Users\costycnc\Documents\Arduino\libraries\grbl_costycnc_1.1\cpu_map.h (1 hit)
		Line 38:   #define STEP_PORT       PORTD
	  C:\Users\costycnc\Documents\Arduino\libraries\grbl_costycnc_1.1\stepper.c (8 hits)
		Line 331:     //st.step_bits = (STEP_PORT & ~STEP_MASK) | st.step_outbits; // Store out_bits to prevent overwriting.
		Line 336:     //STEP_PORT = (STEP_PORT & ~STEP_MASK) | st.step_outbits;
		Line 336:     //STEP_PORT = (STEP_PORT & ~STEP_MASK) | st.step_outbits;
		Line 492:   //STEP_PORT = (STEP_PORT & ~STEP_MASK) | (step_port_invert_mask & STEP_MASK);
		Line 492:   //STEP_PORT = (STEP_PORT & ~STEP_MASK) | (step_port_invert_mask & STEP_MASK);
		Line 506:     //STEP_PORT = st.step_bits; // Begin step pulse.
		Line 554:   //STEP_PORT = (STEP_PORT & ~STEP_MASK) | step_port_invert_mask;
		Line 554:   //STEP_PORT = (STEP_PORT & ~STEP_MASK) | step_port_invert_mask;
	      
sys_position[X_AXIS]--;


      costyx=costyx-1;
     if (costyx < 1) costyx=8;
     if (costyx==1)   PORTD=0B100000;
     if (costyx==2)   PORTD=0B110000;
     if (costyx==3)   PORTD=0B010000;
     if (costyx==4)   PORTD=0B011000;
     if (costyx==5)  PORTD=0B001000;
     if (costyx==6)  PORTD=0B001100;
     if (costyx==7)  PORTD=0B000100;
     if (costyx==8) PORTD=0B100100;
     
sys_position[X_AXIS]++;

      costyx=costyx+1;
     if (costyx > 8) costyx=1;
     if (costyx==1)   PORTD=0B100000;
     if (costyx==2)   PORTD=0B110000;
     if (costyx==3)   PORTD=0B010000;
     if (costyx==4)   PORTD=0B011000;
     if (costyx==5)  PORTD=0B001000;
     if (costyx==6)  PORTD=0B001100;
     if (costyx==7)  PORTD=0B000100;
     if (costyx==8) PORTD=0B100100;
