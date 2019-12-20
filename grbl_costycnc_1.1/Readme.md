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

change cpu_map.h
	      
Tutorial:

		 #define X_STEP_BIT      2 
		 int buttonState = B0;
		 buttonState |= (1<<X_STEP_BIT);
		 Serial.println(buttonState,BIN);
		 Result > 100     (4)
	      
	      PORTD = PORTD & B00000011;  // clear out bits 2 - 7, leave pins PD0 and PD1 untouched (xx & 11 == xx)
	      DDRD = DDRD | B11111100;    // set out bits 2 - 7, leave pins PD0 and PD1 untouched (xx | 00 == xx)
	      
Begin modify 20.12.2019	     

stepper.c

		#include "grbl.h"
		int costyx=1;
		int costyy=1;

--

             //rotate motor stanga sau dreapta

		void rotate_stanga()
		{
		 sys.position[X_AXIS]--;
		      //
		      //UDR0=65;
		      //DDRD=255;
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

			 }
	 
	 void rotate_dreapta()
	 {
		sys.position[X_AXIS]++;
     // UDR0=66;
      //DDRD=255;
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
		  }
----
       .
       .
       .
       .
       .
       .
       

              if (st.exec_block->direction_bits & (1<<X_DIRECTION_BIT)) { sys_position[X_AXIS]--; }
              else { sys_position[X_AXIS]++; }
	      
	      mai jos cu subrutina ...
	      
	      if (st.exec_block->direction_bits & (1<<X_DIRECTION_BIT)) { rotate_stanga; }
              else { rotate_dreapta; }
	      
	      

     

main.c

     int main(void)
    {
          DDRD=0B111100;
          DDRC=0B00001111;
	  
stepper.c	  

		  void st_go_idle() 
		{
		.
		.
		.
		delay_ms(settings.stepper_idle_lock_time);
		PORTD=0;
		PORTC=0;	  
	  
