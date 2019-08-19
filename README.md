# STM32 NUCLEO-F767ZI USB CDC Example

It finally connects and provides Virtual COM port !!!!

* red LED D3 lights on fatal error
* blue LED D2 is blinking (to signal that CPU is running)


WARNING! The Code Generated by CubeMX is buggy - you must
apply this fix:

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
