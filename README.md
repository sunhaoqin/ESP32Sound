# ESP32Sound
Arduino library for audio playback on ESP32 / ODROID-GO platforms.

This library provides sound capabilites for the ODROID-GO. 
It supports playback of large sound files from SD card (background music)
and concurrent playback of short sound effects (FX) from flash memory.
The volume of background music and FX can be set separately.
Sound playback works "in background" so that the main loop can be
used for other things (LCD, buttons, WiFi etc.) 
Please note that the sampling rate of background music and FX files must be the same.
Although playSound() can handle different .wav files (eg. 44,1Khz, 16bit, stereo), 
the format 16Khz, mono, 8 bit is recommended (low-bandwidth, low CPU-load).
The provided python scripts can be used to convert arbitrary .wav files to that format.

### Preparation/placement of sound files  
The sound files must be provided in .wav format.  

The background music files can be placed on the SD card, the file path is given to the
*playSound()* function as an argument with a leading slash, eg. *playSound(SD, "/myfile.wav");*

The FX files are stored in flash memory as a C-array. Only small files should be used.
The python script *wav2array.py* converts .wav files into C-arrays, it creates the header file 
*sounds.h* from one or more .wav files, eg: ***python raw2array.py sound1.wav sound2.wav sound3.wav***
After including *sounds.h* into your sketch, you can use *playFX(sound1)*, *playFX(sound2)*, ...
The python script *wav2wav.py* converts .wav files to 16Khz, mono, 8 bit format.


### Implementation infos  
The current version of the library uses a timer ISR (called with sampling rate) for feeding the sound-samples 
into the DAC of the ODROID-GO. A dedicated task refills the play queue with sound data from the SD card. 
The soundfile is read in chunks of 512 values. This enables a continuous playback without breaks / pops 
even if concurrent LCD traffic is ongoing. (Please use a small delay(10) in the main loop to provide sufficient 
SPI bandwith for sound transfers in case of heavy LCD action ...)
 
Future improvements might use DMA/I2S to increase performance.

This code is released under GPLv3 license.
see: https://www.gnu.org/licenses/gpl-3.0.en.html and https://www.fsf.org/


