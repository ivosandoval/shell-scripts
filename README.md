shell-scripts
=============

This repository contains some shell scripts that might be useful.

arminfo
-------

This script shows some information about the processor, if you run it on an ARM machine.

Example output:

	dword@raspberrypi[~]$ ./arminfo 
	ARM Ltd. ARMv7 Processor
	ARM1176 r0p7

diskstatplus
------------

Monitor how much bytes you have read / written to disks that are currently connected on the computer.
Information is gathered from **/proc/diskstats**.

Example output:

	dword@prime:~$ ./diskstatsplus 
	Deivce      Read IEC   Write IEC  Ops      Wall Time  Weighted Time
	========
	sda        657.649 G   994.669 G  0      1:22:13.743   11:22:43.807
	sda1       403.606 G   860.938 G  0      1:09:10.112   10:54:23.991
	sda2       254.012 G   133.731 G  0      0:13:06.251    0:28:16.172
	sdb        413.767 G   375.003 G  0      5:08:03.191   14:00:04.300
	sdb1       412.581 G   374.826 G  0      4:52:17.324   13:43:08.811
	sdb2         1.021 G   180.543 M  0      0:00:04.604    0:00:09.243
	sdb3       109.237 M   589.000 K  0      0:00:00.460    0:00:00.524
	sdc          9.058 G    16.071 G  0      0:07:03.172    8:17:40.880
	sdc1         9.057 G    16.071 G  0      0:07:03.019    8:17:40.727

You can also pass a integer argument to the script to enter the monitoring mode.
The integer controls how many seconds to wait between each update.

gen\_oui\_table.sh
------------------

Grab the latest OUI data from IEEE's website and turn it into a C header.
You also get the data as well.

Example output (the header):

	#ifndef __OUI_H__
	#define __OUI_H__
	
	#ifndef OUI_VENDOR_STR_SIZE
	#define OUI_VENDOR_STR_SIZE 128
	#endif /* OUI_VENDOR_STR_SIZE */
	
	#include <stdint.h>
	
	struct oui_entry {
	  /* OUI Prefix, bytes 1-3 in MAC Addresses */
	  uint8_t mac1;
	  uint8_t mac2;
	  uint8_t mac3;

	  /* Vendor name */
	  char vendor[OUI_VENDOR_STR_SIZE];
	} oui_table[] = {
	  {0x00, 0x00, 0x00, "XEROX CORPORATION"},
	  .......
	  {0xFC, 0xFF, 0xAA, "IEEE REGISTRATION AUTHORITY  - Please see MAL public listing for more information."},
	};
	
	#define OUI_SIZE (sizeof(oui_table) / sizeof(oui_table[0]))
	
	#endif /* __OUI_H__ */

gitstat
-------

Gives numbers for total commits and modifications.

Example output:

	$ ./gitstat
	13 commits, 910 insertions, 12 deletions, 922 total.

mmcinfo
-------

Gathers information about you SD card, and try to identify the manufacturer.
You will need a native MMC reader (not a USB adapter).
PCIe readers should work fine, and you can also run this script on your Android device,
or SBCs such as Raspberry Pi or Beaglebone.

Example output:

	$ ./mmcinfo
	MMC Host Controller "mmc0":
	  Driver: rk29_sdmmc
	  Alias : platform:rk29_sdmmc
	  =========================================
	  Card @ 0x1234:
	    Card Type             : SD
	    Card Name             : SA02G
	    Card Size             : 1882 MiB
	    Card Serial           : 0x12685af7
	    Block Device          : mmcblk0
	    OEM ID                : 0x544d (TM)
	    Manufacturer ID       : 0x000002 (TOSHIBA)
	    Manufacture Date      : 12/2010
	    Hardware Revision     : 0x0
	    Firmware Revision     : 0x5
	    Product Revision      : N/A
	    Prefered Erase Size   : 4096 KiB
	    Mode                  : Block-Addressed
	    CID Register          : 02544d53413032470512685af700ac65
	    CSD Register          : 002e00325b5aa3acffffff800a80007f
	    SCR Register          : 0225800001000000
	  =========================================
	
	MMC Host Controller "mmc1":
	  Driver: rk29_sdmmc
	  Alias : platform:rk29_sdmmc
	  =========================================
	  Card @ 0x0001:
	    Card Type             : SDIO
	    Function 1:
	      Class               : 0x00
	      Alias               : sdio:c00v02D0dA962
	      Vendor              : 0x02d0
	      Device              : 0xa962
	      Driver              : bcmsdh_sdmmc
	    Function 2:
	      Class               : 0x00
	      Alias               : sdio:c00v02D0dA962
	      Vendor              : 0x02d0
	      Device              : 0xa962
	      Driver              : bcmsdh_sdmmc
	  =========================================

The second device is a BCM43326. Perhaps I will add identification for those devices as well, in the future.

texcat.sh
---------

Convert large LaTeX projects into plain text for spell and grammar checking.
Currently work-in-progress.

thermal.sh
----------

Read temperature on Raspberry Pis.
Also works on ACPI (x86) machines but most of them have proper hwmon drivers anyway.

Example output:

	$ ./thermal.sh
	zone0:  57.3°C (bcm2835_thermal)

	$ ./thermal.sh
	zone0:  67.0°C (x86_pkg_temp, passive = 0.0°C, passive = 0.0°C)

trimverify.sh
-------------

(Slowly) verify whether a file or directory is affected by certain SSD TRIM bugs.
May have lots of false positives, especially for certain file types.

uartled.sh
----------

This script uses a TTL UART port to control a LED for you.
After all, "U" stands for "universal".

However, you do need a few extra components: a LED,
a NPN transistor (PN2222A used here),
2 resistors (one for the LED and the other for the base of the transistor,
recommend 100R and 1k, respectively),
a capacitor that is about 2.2-22uF (depending on the base resistor),

Connect the things as following:

	Vcc --[+ LED -]--[100R resistor]--/C|   ____________
	TX  --------[1k resistor]--------( B|--/+ 2.2uF    /\
	GND ------------------------------\E|--\- capacitor\/
	                                PN2222A

The capacitor eliminates high frequency components from the UART line,
so the LED does not appear to be on during the off period.

Then, call the script like this:

	./uartled.sh /dev/ttyUSB0 500 1000

The LED will be turned on for approximately 500ms,
then turned off for approximately 1000ms.

The idea arose when the laboratory ran out of Arduinos for some LED related sensing project.
I do have a lot of USB to UART bridges.
