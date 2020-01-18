      rotate stanga dreapta stepper.cpp
      initial values default.h
      in report.h 
      initial 
      
// Grbl global settings print out.
// NOTE: The numbering scheme here must correlate to storing in settings.c
void report_grbl_settings() {
  printPgmString(PSTR("$0=")); printFloat(settings.steps_per_mm[X_AXIS]);
  printPgmString(PSTR(" (x, step/mm)\r\n$1=")); printFloat(settings.steps_per_mm[Y_AXIS]);
  printPgmString(PSTR(" (y, step/mm)\r\n$2=")); printFloat(settings.steps_per_mm[Z_AXIS]);
  printPgmString(PSTR(" (z, step/mm)\r\n$3=")); printInteger(settings.pulse_microseconds);
  printPgmString(PSTR(" (step pulse, usec)\r\n$4=")); printFloat(settings.default_feed_rate);
  printPgmString(PSTR(" (default feed, mm/min)\r\n$5=")); printFloat(settings.default_seek_rate);
  printPgmString(PSTR(" (default seek, mm/min)\r\n$6=")); printInteger(settings.invert_mask); 
  printPgmString(PSTR(" (step port invert mask, int:")); print_uint8_base2(settings.invert_mask);  
  printPgmString(PSTR(")\r\n$7=")); printInteger(settings.stepper_idle_lock_time);
  printPgmString(PSTR(" (step idle delay, msec)\r\n$8=")); printFloat(settings.acceleration/(60*60)); // Convert from mm/min^2 for human readability
  printPgmString(PSTR(" (acceleration, mm/sec^2)\r\n$9=")); printFloat(settings.junction_deviation);
  printPgmString(PSTR(" (junction deviation, mm)\r\n$10=")); printFloat(settings.mm_per_arc_segment);
  printPgmString(PSTR(" (arc, mm/segment)\r\n$11=")); printInteger(settings.n_arc_correction);
  printPgmString(PSTR(" (n-arc correction, int)\r\n$12=")); printInteger(settings.decimal_places);
  printPgmString(PSTR(" (n-decimals, int)\r\n$13=")); printInteger(bit_istrue(settings.flags,BITFLAG_REPORT_INCHES));
  printPgmString(PSTR(" (report inches, bool)\r\n$14=")); printInteger(bit_istrue(settings.flags,BITFLAG_AUTO_START));
  printPgmString(PSTR(" (auto start, bool)\r\n$15=")); printInteger(bit_istrue(settings.flags,BITFLAG_INVERT_ST_ENABLE));
  printPgmString(PSTR(" (invert step enable, bool)\r\n$16=")); printInteger(bit_istrue(settings.flags,BITFLAG_HARD_LIMIT_ENABLE));
  printPgmString(PSTR(" (hard limits, bool)\r\n$17=")); printInteger(bit_istrue(settings.flags,BITFLAG_HOMING_ENABLE));
  printPgmString(PSTR(" (homing cycle, bool)\r\n$18=")); printInteger(settings.homing_dir_mask);
  printPgmString(PSTR(" (homing dir invert mask, int:")); print_uint8_base2(settings.homing_dir_mask);  
  printPgmString(PSTR(")\r\n$19=")); printFloat(settings.homing_feed_rate);
  printPgmString(PSTR(" (homing feed, mm/min)\r\n$20=")); printFloat(settings.homing_seek_rate);
  printPgmString(PSTR(" (homing seek, mm/min)\r\n$21=")); printInteger(settings.homing_debounce_delay);
  printPgmString(PSTR(" (homing debounce, msec)\r\n$22=")); printFloat(settings.homing_pulloff);
  printPgmString(PSTR(" (homing pull-off, mm)\r\n")); 
  
  
  actual report.cpp

GRBL-Arduino-Library
====================

Arduino Library for GRBL - This way you can use the Arduino IDE to upload GRBL to your Arduino Board.


How to install it:
==================

- Down load the library from GitHub : https://github.com/Protoneer/GRBL-Arduino-Library/archive/master.zip

- Unzip the library and copy the main folder into the “Libraries” folder in the folder you installed your Arduino software. Eg. C:\arduino-1.0.3\libraries\

- Rename the folder to “GRBL”. (This will stop the Arduino IDE from complaining about the long folder name)

- Open up the Arduino IDE.

- Click on the following menu : File -> Examples – > GRBL (or what ever you renamed the folder to) -> ArduinoUno

- Upload the sketch to your Arduino board.
