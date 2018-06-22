# ota-dfu-smartband
QUICK :

python merge_hex.py bootloader.hex s130_nrf51_2.0.1_softdevice.hex  booter.hex

./upload booter.hex


==build a package==
nrfutil pkg generate --hw-version 51 --application-version 10 --application nrf51422_xxac.hex --sd-req 0x87 --key-file priv_key.pem app_dfu_package.zip


python ./dfu.py -z app_dfu_package.zip -a DA:E2:C9:AB:2F:66 --secure

===upload application over the air===

Note: re-enter in DFU mode by pressing side button

Note: 
to create from the arduino IDE, using Sandeep Mistry his nrf5 core

you have to add this 

if (!B1_isPressed & !digitalRead(PIN_BUTTON1)) // if the side button is pressed 
  {
      sd_power_gpregret_set(0xB1); // write a value to the gpreget register 
      NVIC_SystemReset();  //soft reset the nrf51822
  }

an example is included in this repository KX02xxxxxx.ino

===================================================

OTA DFU nrf51822 smartwatch ID107 arduino


I used a cheap bluetooth 4.0 dongle, and a raspberry pi

I used and stlink-v2 dongle to program the id107 watch (nrf51822) - openocd 

----download the SDK 12.3 from nordic

https://developer.nordicsemi.com/nRF5_SDK/nRF5_SDK_v12.x.x/


----modify secure_dfu_gcc_nrf51.ld (in this repository)

nRF5_SDK_12.3.0_d7731ad/examples/dfu/bootloader_secure/pca10028/armgcc
secure_dfu_gcc_nrf51.ld

----modify components/boards/pca10028.h:#define BUTTON_4       4 //side button watch

----follow this procedure

https://devzone.nordicsemi.com/b/blog/posts/getting-started-with-nordics-secure-dfu-bootloader


https://github.com/NordicSemiconductor/pc-nrfutil

nrfutil keys display --key pk --format code priv_key.pem > dfu_public_key.c


---- generate private and public key
---- copy public key to nRF5_SDK_12.3.0_d7731ad/examples/dfu/bootloader_secure
---- modify makefile in order to point to dfu_public_key.c



python merge_hex.py nrf51422_xxac_s130.hex s130_nrf51_2.0.1_softdevice.hex  booter.hex
./upload booter.hex

---- merge the bootloader (compiled under _build directory) 
---- upload the bootloader to nrf51822



nrfutil pkg generate --hw-version 51 --application-version 10 --application nrf51422_xxac.hex --sd-req 0x87 --key-file priv_key.pem app_dfu_package.zip

---- generate a package ( sd-req 0x87 is for softdevice 2.0.1 )



git clone https://github.com/dingari/ota-dfu-python.git


python ./dfu.py -z app_dfu_package.zip -a DA:E2:C9:AB:2F:66 --secure

---- upload package over the air!

TODO :
------------------
once uploaded the smartwatch is no longer in dfu mode
you can switch back to bootloader mode by holding the side button of the watch for 10 seconds



since I want to use to button on the watch for other purposes 
-> Buttonless DFU would be handy


