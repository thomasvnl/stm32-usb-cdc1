# STM32 NUCLEO-F767ZI USB CDC Example

It finally connects to Host and provides Virtual COM port (for `User USB` connector on Nucleo board).

NOTE: There are two Virtual COM ports:

1. `CN1`-`USB PWR` - this is ST-Link connector. It provides
   always Virtual port named `STMicroelectronics STLink Virtual COM Port (COMx)` under Windows. 

   Under Linux it is visible like (output from `dmesg`):
   ```
   usb 2-1: new full-speed USB device number 2 using ohci-pci
   usb 2-1: New USB device found, idVendor=0483, idProduct=374b
   usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
   usb 2-1: Product: STM32 STLink
   usb 2-1: Manufacturer: STMicroelectronics
   usb 2-1: SerialNumber: xxxxxxxxxxxxxxxxxxxxxxxx
   cdc_acm 2-1:1.2: ttyACM0: USB ACM device
   usbcore: registered new interface driver cdc_acm
   cdc_acm: USB Abstract Control Model driver for USB modems and ISDN adapters
   ```

   Please notice `idVendor=0483, idProduct=374b`

   This port is connected to `USART1` of MCU. If you want to use this serial port
   you can look into example in:

   ```
   ~/STM32Cube/Repository/STM32Cube_FW_F7_V1.15.0/Projects/STM32F767ZI-Nucleo/Examples/UART/`
   ```
   provided by vendor.



1. `CN13`-`User USB` - this the USB directly attached to MCU.
   It is visible under Windows 
   as `STMicroelectronics Virtual COM Port (COMx)`. This port is
   used in this project.

   Please notice different "idProduct" ("idVendor" is same as above): \\
   `idVendor=0483, idProduct=5740`


Current function:

* red LED D3 lights on fatal error
* blue LED D2 is blinking (to signal that CPU is running)
* green LED D1 - you can control it by sending `1` or `0` character
  to Virtual COM port on `CN12`-`User USB`. All other characters
  are currently silently ignored.


WARNING! The Code Generated by `CubeMX 5.3.0` is buggy (at least
for firmware `STM32Cube_FW_F7_V1.15.0`) - you must
apply this fix to generated code:

```diff
--- a/Src/usbd_conf.c
+++ b/Src/usbd_conf.c
@@ -206,11 +206,11 @@ void HAL_PCD_ResetCallback(PCD_HandleTypeDef *hpcd)
 { 
   USBD_SpeedTypeDef speed = USBD_SPEED_FULL;
 
-  if ( hpcd->Init.speed == PCD_SPEED_HIGH)
+  if ( hpcd->Init.speed == USB_OTG_SPEED_HIGH)
   {
     speed = USBD_SPEED_HIGH;
   }
-  else if ( hpcd->Init.speed == PCD_SPEED_FULL)
+  else if ( hpcd->Init.speed == USB_OTG_SPEED_FULL)
   {
     speed = USBD_SPEED_FULL;
   }
```

Also in my case I need to replace:
* `__packed="__attribute__((__packed__))"`
with
* `__packed="__attribute__((packed))"`
to avoid build errors (in C/C++ Build -> Settings -> MCU GCC
Compiler -> Preprocessor).

NOTE: To avoid problems please use this directory structure:
* `/home/ansible/projects/stm32-usb-cdc1/` - path of this project
* `/home/ansible/STM32Cube/Repository/STM32Cube_FW_F7_V1.15.0/` - path
  of firmware package `STM32Cube_FW_F7_V1.15.0`

Used Software:
* OS: Debian Linux 9.9/amd64
* CubeMX 5.3.0, installer: `en.STM32CubeMX_v5-3-0.zip`
* Firmware: `STM32Cube_FW_F7_V1.15.0`
* IDE: SW4STM32 v2.9 for Linux/64-bit, installer: `install_sw4stm32_linux_64bits-v2.9.run`

