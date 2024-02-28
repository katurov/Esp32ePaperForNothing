# Esp32 ePaper  For Nothing

Uses [GxEPD2 module](https://github.com/ZinggJM/GxEPD2) in Arduino IDE

## Part list
* ESP32 DEVKIT v1
* WeAct Studio 2.13" ePaper Module 4-lines SPI 3 colors

## Pinout and wiring

[Driver](https://github.com/ZinggJM/GxEPD2) uses "system" SPI lines if other not defined, so no need to assign them in the code, next is the wiring

| Display | ESP32 | Arduino IDE code |
| --- | --- | ---- |
| Busy | D4 | 4 |
| Res | D2 | 2 |
| D/C | D15 | 15 |
| CS | SS (D5) | 5 |
| SCL | SCK (D18) | None |
| SDA | MOSI (D23) | None |
| Gnd | Ground | None |
| VCC | 3v3 | None |

All these pins (```Arduino IDE code```) have to be defined in ```GxEPD2_display_selection_new_style.h``` as in [example](https://github.com/ZinggJM/GxEPD2/blob/4f3c96778e9ff9471a78ce7affa05bbb7a5991b0/examples/GxEPD2_HelloWorld/GxEPD2_HelloWorld.ino) :
* uncomment :
```C
#define GxEPD2_DRIVER_CLASS GxEPD2_213_Z98c // GDEY0213Z98 122x250, SSD1680, (FPC-A002 20.04.08)
```
* check this code, mind the device you profiling :
```C
#if defined(ESP32)

  #define MAX_DISPLAY_BUFFER_SIZE 65536ul // e.g.
  
  #if IS_GxEPD2_BW(GxEPD2_DISPLAY_CLASS)
    #define MAX_HEIGHT(EPD) (EPD::HEIGHT <= MAX_DISPLAY_BUFFER_SIZE / (EPD::WIDTH / 8) ? EPD::HEIGHT : MAX_DISPLAY_BUFFER_SIZE / (EPD::WIDTH / 8))
  #elif IS_GxEPD2_3C(GxEPD2_DISPLAY_CLASS) || IS_GxEPD2_4C(GxEPD2_DISPLAY_CLASS)
    #define MAX_HEIGHT(EPD) (EPD::HEIGHT <= (MAX_DISPLAY_BUFFER_SIZE / 2) / (EPD::WIDTH / 8) ? EPD::HEIGHT : (MAX_DISPLAY_BUFFER_SIZE / 2) / (EPD::WIDTH / 8))
  #elif IS_GxEPD2_7C(GxEPD2_DISPLAY_CLASS)
    #define MAX_HEIGHT(EPD) (EPD::HEIGHT <= (MAX_DISPLAY_BUFFER_SIZE) / (EPD::WIDTH / 2) ? EPD::HEIGHT : (MAX_DISPLAY_BUFFER_SIZE) / (EPD::WIDTH / 2))
  #endif

  // adapt the constructor parameters to your wiring
  #if !IS_GxEPD2_1248(GxEPD2_DRIVER_CLASS) && !IS_GxEPD2_1248c(GxEPD2_DRIVER_CLASS)
    
    #if defined(ARDUINO_LOLIN_D32_PRO)
      GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS=5*/ EPD_CS, /*DC=*/ 0, /*RST=*/ 2, /*BUSY=*/ 15)); // my LOLIN_D32_PRO proto board
    #elif defined(ARDUINO_LOLIN_S2_MINI)
      GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS*/ 33, /*DC=*/ 35, /*RST=*/ 37, /*BUSY=*/ 39)); // my LOLIN ESP32 S2 mini connection
    #else
      GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS=5*/ EPD_CS, /*DC=*/ 15, /*RST=*/ 2, /*BUSY=*/ 4)); // my suggested wiring and proto board
      //GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS=5*/ 5, /*DC=*/ 17, /*RST=*/ 16, /*BUSY=*/ 4)); // LILYGO_T5_V2.4.1
      //GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS=5*/ EPD_CS, /*DC=*/ 19, /*RST=*/ 4, /*BUSY=*/ 34)); // LILYGO® TTGO T5 2.66
      //GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS=5*/ EPD_CS, /*DC=*/ 2, /*RST=*/ 0, /*BUSY=*/ 4)); // e.g. TTGO T8 ESP32-WROVER
      //GxEPD2_DISPLAY_CLASS<GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS)> display(GxEPD2_DRIVER_CLASS(/*CS=*/ 15, /*DC=*/ 27, /*RST=*/ 26, /*BUSY=*/ 25)); // Waveshare ESP32 Driver Board
    #endif
  
  #else // GxEPD2_1248 or GxEPD2_1248c
    // Waveshare 12.48 b/w or b/w/r SPI display board and frame or Good Display 12.48 b/w panel GDEW1248T3 or b/w/r panel GDEY1248Z51
    // general constructor for use with all parameters, e.g. for Waveshare ESP32 driver board mounted on connection board
    GxEPD2_DISPLAY_CLASS < GxEPD2_DRIVER_CLASS, MAX_HEIGHT(GxEPD2_DRIVER_CLASS) > display(GxEPD2_DRIVER_CLASS(/*sck=*/ 13, /*miso=*/ 12, /*mosi=*/ 14,
        /*cs_m1=*/ 23, /*cs_s1=*/ 22, /*cs_m2=*/ 16, /*cs_s2=*/ 19,
        /*dc1=*/ 25, /*dc2=*/ 17, /*rst1=*/ 33, /*rst2=*/ 5,
        /*busy_m1=*/ 32, /*busy_s1=*/ 26, /*busy_m2=*/ 18, /*busy_s2=*/ 4));
  #endif

  #undef MAX_DISPLAY_BUFFER_SIZE
  #undef MAX_HEIGHT

#endif
```
