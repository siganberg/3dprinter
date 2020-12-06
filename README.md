**Noted:** All instruction  here to send GCODE command to the printer are performed remotely on the Terminal Command tab of OctoPrint. For me, this is the most convinient method. You are missing a lot if you don't use OctoPrint by the way. :D

There some other way to send GCode to the printer. 

## Adjusting the BLTouch Offset

- To determine your z-offset, you can refer to this guide: https://www.youtube.com/watch?v=y_1Kg45APko&t=2s
- The key command for adjusting the z-offsets is: `M851 Z{somevalue}`. Then save it to EEFROM using `M500`. `M501` to restore it from EEFROM.  
- Once you have the Z-offset, including X and Y you can actually save this to the `configuration.h` for future flashing. This save you redoing the previous steps. Z-offset doesn't change unless your change your BLTouch mouting. Just make sure you have the right value of X, Y and most importantly the Z. For X and Y you can measure this with caliper but X=48 and Y=2 should be the ballpark for stock CR-10 V2 and Creality BLTouch version (including the metal mounting), this is not  senstive compare to Z-offset.  The `-2.40` on the example is my Z offset. 

```
//-- search for this param in configuration.h and adjust 
#define NOZZLE_TO_PROBE_OFFSET { 48, 2, -2.40 } 
```

## Instruction after flashing

This is important. If you don't do this after flashing, your printer won't have mesh data and executing GCODE `M420` during printing (startup script) will halt your printer and will ask you to power cycle your printer. 

- Perform Auto Home: `G28`
- Run bed leveling to initial mesh data. `G29` 
- Wait for the bed leveling to complete. It will perform 9 points probing. 
- After bed leveling save the mesh output into EEFROM: `M500`
- Restore settings from EEFROM:  `M501`
- Since my preference of leveling is on-demand, you need to adjust your startup GCODE for all of your print. I replaced the `G29` with this GCode on my startup script `M420 S1 Z5; restore mesh offset from last auto-level`
- You don't need to do this if your preference is to run the bed leveling on every prints. Keep the `G29` on your startup script of everyprint. 

## GCode Useful command
G1 F60 Z0 - Move Z axis on the speed of 60 (slow)
