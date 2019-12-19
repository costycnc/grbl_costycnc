# grbl_costycnc

Below is for grbl 0.8c For grbl 1.1 see grbl_costycnc_1.1 

grbl modified for costycnc (also here  https://sites.google.com/site/costycnc/modify-grbl)

for compile with arduino ...put this folder in documents/arduino/libraries ... open arduino ide
in file ... examples ... will find GRBL_costy >> GRBLtoArduino ... open it 

or open Documents\Arduino\libraries\GRBL_costy\examples\GRBLtoArduino >>GRBLtoArduino.ino

Board:Arduino nano
Processor:Atmega328


protocol.c >> protocol_process()

first grbl 2009 >> https://github.com/grbl/grbl/commits/master?after=3ce1a9d637f05e28462a36cb8b166386aab94afc+600

Go to arduino libraries grbl and open config.h

and modify (see diferencies):

```C
 /*
  config.h - compile time configuration
  Part of Grbl

 

#ifndef config_h
#define config_h

// IMPORTANT: Any changes here requires a full re-compiling of the source code to propagate them.

// Default settings. Used when resetting EEPROM. Change to desired name in defaults.h
#define DEFAULTS_GENERIC

// Serial baud rate
#define BAUD_RATE 9600

// Define pin-assignments
// NOTE: All step bit and direction pins must be on the same port.
#define STEPPING_DDR       DDRB
#define STEPPING_PORT      PORTB
#define X_STEP_BIT         0  // Uno Digital Pin 2
#define Y_STEP_BIT         0  // Uno Digital Pin 3
#define Z_STEP_BIT         0//4  // Uno Digital Pin 4
#define X_DIRECTION_BIT    6//5  // Uno Digital Pin 5
#define Y_DIRECTION_BIT    7//6  // Uno Digital Pin 6
#define Z_DIRECTION_BIT    0//7  // Uno Digital Pin 7
#define STEP_MASK ((1<<X_STEP_BIT)|(1<<Y_STEP_BIT)|(1<<Z_STEP_BIT)) // All step bits
#define DIRECTION_MASK ((1<<X_DIRECTION_BIT)|(1<<Y_DIRECTION_BIT)|(1<<Z_DIRECTION_BIT)) // All direction bits
#define STEPPING_MASK (STEP_MASK | DIRECTION_MASK) // All stepping-related bits (step/direction)

#define STEPPERS_DISABLE_DDR    DDRB
#define STEPPERS_DISABLE_PORT   PORTB
#define STEPPERS_DISABLE_BIT    5//0  // Uno Digital Pin 8
#define STEPPERS_DISABLE_MASK (1<<STEPPERS_DISABLE_BIT)

// NOTE: All limit bit pins must be on the same port
#define LIMIT_DDR       DDRB
#define LIMIT_PIN       PINB
#define LIMIT_PORT      PORTB
#define X_LIMIT_BIT     1//1  // Uno Digital Pin 9
#define Y_LIMIT_BIT     1//2  // Uno Digital Pin 10
#define Z_LIMIT_BIT     1//3  // Uno Digital Pin 11
#define LIMIT_INT       PCIE0  // Pin change interrupt enable pin
#define LIMIT_INT_vect  PCINT0_vect 
#define LIMIT_PCMSK     PCMSK0 // Pin change interrupt register
#define LIMIT_MASK ((1<<X_LIMIT_BIT)|(1<<Y_LIMIT_BIT)|(1<<Z_LIMIT_BIT)) // All limit bits

#define SPINDLE_ENABLE_DDR   DDRB
#define SPINDLE_ENABLE_PORT  PORTB
#define SPINDLE_ENABLE_BIT   0//4  // Uno Digital Pin 12

#define SPINDLE_DIRECTION_DDR   DDRB
#define SPINDLE_DIRECTION_PORT  PORTB
#define SPINDLE_DIRECTION_BIT   0  // Uno Digital Pin 13 (NOTE: D13 can't be pulled-high input due to LED.)

#define COOLANT_FLOOD_DDR   DDRB
#define COOLANT_FLOOD_PORT  PORTB
#define COOLANT_FLOOD_BIT   0  // Uno Analog Pin 3

// NOTE: Uno analog pins 4 and 5 are reserved for an i2c interface, and may be installed at
// a later date if flash and memory space allows.
// #define ENABLE_M7  // Mist coolant disabled by default. Uncomment to enable.
#ifdef ENABLE_M7
  #define COOLANT_MIST_DDR   DDRB
  #define COOLANT_MIST_PORT  PORTB
  #define COOLANT_MIST_BIT   0 // Uno Analog Pin 4
#endif  

// NOTE: All pinouts pins must be on the same port
#define PINOUT_DDR       DDRB
#define PINOUT_PIN       PINB
#define PINOUT_PORT      PORTB
#define PIN_RESET        1  // Uno Analog Pin 0
#define PIN_FEED_HOLD    1  // Uno Analog Pin 1
#define PIN_CYCLE_START  1  // Uno Analog Pin 2
#define PINOUT_INT       PCIE1  // Pin change interrupt enable pin
#define PINOUT_INT_vect  PCINT1_vect
#define PINOUT_PCMSK     PCMSK1 // Pin change interrupt register
#define PINOUT_MASK ((1<<PIN_RESET)|(1<<PIN_FEED_HOLD)|(1<<PIN_CYCLE_START))
```
open stepper.cpp and modify

```C
 /*
  stepper.c - stepper motor driver: executes motion plans using stepper motors
  Part of Grbl

  Copyright (c) 2009-2011 Simen Svale Skogsrud
  Copyright (c) 2011-2012 Sungeun K. Jeon
  
  Grbl is free software: you can redistribute it and/or modify
  it under the terms of the GNU General Public License as published by
  the Free Software Foundation, either version 3 of the License, or
  (at your option) any later version.

  Grbl is distributed in the hope that it will be useful,
  but WITHOUT ANY WARRANTY; without even the implied warranty of
  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
  GNU General Public License for more details.

  You should have received a copy of the GNU General Public License
  along with Grbl.  If not, see <http://www.gnu.org/licenses/>.
*/

/* The timer calculations of this module informed by the 'RepRap cartesian firmware' by Zack Smith
   and Philipp Tiefenbacher. */

#include <avr/interrupt.h>
#include "stepper.h"
#include "config.h"
#include "settings.h"
#include "planner.h"
int costyx=1;
int costyy=1;

-------------------------
-------------------------

if (sys.state == STATE_CYCLE) {
        // During feed hold, do not update rate and trap counter. Keep decelerating.
        st.trapezoid_adjusted_rate = current_block->initial_rate;
        set_step_events_per_minute(st.trapezoid_adjusted_rate); // Initialize cycles_per_step_event
        st.trapezoid_tick_cycle_counter = CYCLES_PER_ACCELERATION_TICK/2; // Start halfway for midpoint rule.
      }
      st.min_safe_rate = current_block->rate_delta + (current_block->rate_delta >> 1); // 1.5 x rate_delta
      st.counter_x = -(current_block->step_event_count >> 1);
      st.counter_y = st.counter_x;
      st.counter_z = st.counter_x;
      st.event_count = current_block->step_event_count;
      st.step_events_completed = 0;     
    } else {
      st_go_idle();
      bit_true(sys.execute,EXEC_CYCLE_STOP); // Flag main program for cycle end
    }    
  } 

  if (current_block != NULL) {
    // Execute step displacement profile by bresenham line algorithm
    out_bits = current_block->direction_bits;
    st.counter_x += current_block->steps_x;
    if (st.counter_x > 0) {
      out_bits |= (1<<X_STEP_BIT);
      st.counter_x -= st.event_count;
      if (out_bits & (1<<X_DIRECTION_BIT)  ) { sys.position[X_AXIS]--;
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
      else { sys.position[X_AXIS]++;
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

    }

    st.counter_y += current_block->steps_y;
    if (st.counter_y > 0) {
      out_bits |= (1<<Y_STEP_BIT);
      st.counter_y -= st.event_count;
      if (out_bits & (1<<Y_DIRECTION_BIT)) { sys.position[Y_AXIS]--;
      //DDRB=255;
      costyy=costyy-1;
      if (costyy < 1) costyy=8;
      if (costyy==1)   PORTC=0B1000;
      if (costyy==2)   PORTC=0B1100;
      if (costyy==3)   PORTC=0B0100;
      if (costyy==4)   PORTC=0B0110;
      if (costyy==5)  PORTC=0B0010;
      if (costyy==6)  PORTC=0B0011;
      if (costyy==7)  PORTC=0B0001;
      if (costyy==8) PORTC=0B1001;
      }
      else { sys.position[Y_AXIS]++;
      //DDRB=255;
      costyy=costyy+1;
     if (costyy > 8) costyy=1;
     if (costyy==1)   PORTC=0B1000;
     if (costyy==2)   PORTC=0B1100;
     if (costyy==3)   PORTC=0B0100;
     if (costyy==4)   PORTC=0B0110;
     if (costyy==5)  PORTC=0B0010;
     if (costyy==6)  PORTC=0B0011;
     if (costyy==7)  PORTC=0B0001;
     if (costyy==8) PORTC=0B1001;
      }

    }
    st.counter_z += current_block->steps_z;
    if (st.counter_z > 0) {
      out_bits |= (1<<Z_STEP_BIT);
      st.counter_z -= st.event_count;
      if (out_bits & (1<<Z_DIRECTION_BIT)) { sys.position[Z_AXIS]--; }
      else { sys.position[Z_AXIS]++; }
    }
    
    st.step_events_completed++; // Iterate step events

    // While in block steps, check for de/ac-celeration events and execute them accordingly.
    if (st.step_events_completed < current_block->step_event_count) {
      if (sys.state == STATE_HOLD) {

```

you can see this forum https://forum.arduino.cc/index.php?topic=208099.0
