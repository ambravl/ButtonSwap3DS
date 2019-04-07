# Limitations
* I don't hook the IR service yet, so no zl/zr or c-stick. However, it is why I started this, so I'll be looking into it.
* Home and Power cannot be mapped the same way.  They are processed by the [MCU](https://3dbrew.org/wiki/Hardware#Auxiliary_Microcontroller_.28MCU.29).
* Not really configurable right now, but can be changed if you know ASM.

## Credits
Memory patching code taken from BootNTR

Shinyquagsire for his writeup on redirecting input over WiFi.

Stary & Kazo for InputRedirector, upon which almost all of this is based.

## Setting up the build environment

You will need a working setup of the devkitARM toolchain for 3DS, follow [this guide](https://www.3dbrew.org/wiki/Setting_up_Development_Environment) for help.

Download [makerom](https://github.com/profi200/Project_CTR/releases)

Download [bannertool](https://github.com/Steveice10/bannertool/releases)

Put the makerom and bannertool files compatible with your OS on the devkitPro/devkitARM/bin folder. You can also leave them in the ButtonSwap3DS folder if it doesn’t work.

Clone [ScenicRoute](https://github.com/Stary2001/ScenicRoute) (or download the zip and extract it)

Clone ButtonSwap3DS (or download the zip and extract it)

Open msys (in Windows) or the command prompt (any OS)

(First of all, to “run” something means typing it on a terminal and pressing enter)

Navigate to your ScenicRoute folder (if you’re using Windows and saved it to [Your Username]/ScenicRoute, you’d run the command: ``cd ~/ScenicRoute``. If you’re not using Windows, you probably know how to use cd)

Run ``make install``

Navigate to your ButtonSwap3DS folder (if you’re using Windows and saved it to [Your Username]/ButtonSwap3DS, you’d run the command: ``cd ~/ButtonSwap3DS``)

Go to the “How to Use” section if you haven’t done your mapping yet.

When your mapping is ready, run ``make``.

![](https://i.imgur.com/JcSftRy.png)

The text you get after running ``make install`` will be different, but should be a success.

  

If you get the following error: ``[EXHEADER ERROR] Invalid FileSystemAccess Name: "SeedDB"``, remove the lines with SeedDB from the files cia.rsf and cia_mode3.rsf (from the ButtonSwap3DS/meta folder)

  

Important: whenever you get an error, you might need to close msys and make a new, fresh ButtonSwap3DS folder with only the things you got from cloning. That’s because, while trying to make your cia, some files get made that might have the mistake in them. (That’s 100% definitely the case if you get the SeedDB error)

## How to Use

(Alternative: use danger229’s [ButtonSwap3DSMaker](https://github.com/danger229/ButtonSwap3dsMaker/releases) instead of following the next steps)

  

For the mappings, edit the injected.s file. If your mapping only contains buttons, write it under the buttons section. If it involves the C-Pad (currently buttons can act as the C-Pad, but the C-Pad can’t be made to act as anything else), write it under the C-Pad section, and if it involves the touchscreen, write it under the touchscreen section. The syntax is in the file, and the mask needed can be found by using the ButtonSwap3DSHelper.jar provided.

As an example, the following file would make it so pressing the A button on your 3DS makes it think you’ve pressed the B button, pressing the SELECT button makes it think you’ve pressed both D-Pad Up and Down, and pressing the START button makes it think you’ve touched the screen at the (50,70) point:

![](https://i.imgur.com/dhFXIwI.png)

Once your injected.s file is modified, you can make your cia. Once your cia is ready,transfer it to your 3DS, install it and run it. If you want to revert your mapping, reboot your 3DS.

  
For further customization, go into the meta folder. You can change the cia’s icon, banner and sound there. You can also edit cia.rsf and cia_mode3.rsf to change its name, description, and UniqueId. **If you want to use different mappings** (for different games, for example), **remember to edit the UniqueId of one of them so you can have both mappings installed as different applications**, instead of having to reinstall the cia every time you want to change mappings.


## Each data field, and how to get it
### Button Masks
This is the data that defines which buttons will activate the remapping and which buttons will be pressed.  Either use the provided program, or manually calculate the mask using [this table](https://www.3dbrew.org/wiki/HID_Shared_Memory#PAD_State).
For example, this code would swap A and B:
```
ldr r4, =0x1
ldr r5, =0x2
bl .button
ldr r4, =0x2
ldr r5, =0x1
bl .button
```
##### The next two data fields are six digit numbers.  The first 3 digits are the Y coordinate data, and the last 3 digits are the X coordinate data.
### Touchscreen Data
The data that will be sent as the touchscreen.  Use the provided tool to generate this data.
### C-pad Data
Data that will be sent as the c-pad.  This data is a bit harder to calculate.  The default value for the C-pad is ```0x800800```.  To calculate this value, you will need to use the developer mode on your calculator.  Xor ```0x800800``` by the value you want the C-pad to have.  For example, if you want to have the C-pad pushed to the right, you would xor ```0x800800``` by ```0x800FFF```, giving you ```0x7FF```.  Pad this with 3 zeroes in front (```0x0007FF```), and you have your data!
