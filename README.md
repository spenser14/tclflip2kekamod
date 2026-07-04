# tclflip2kekamod
A guide to install apps on the TCL Flip 2 with the latest "KEKA" software.

TCL Flip 2 KEKA APK Install Mod Guide
This guide is for the TCL Flip 2 running KEKA firmware. It allows APK installation on KEKA by flashing a
patched system image that bypasses the KEKA framework-level APK install block.
This wipes the phone. Back up photos, music, contacts, and anything else important first.
What This Fix Does
KEKA blocks APK installation inside Android’s framework, specifically in services.jar.
This method:
•
•
•
•
•
•
Bypasses the KEKA APK-install block
Allows APK installation through the browser download notification
Allows Aurora Store to install and run
Does not require working ADB
Does not require Magisk/root
Does not require ADBio
File Manager may still refuse to open APKs. Use the browser download notification instead.
Required Files
Create a folder:
~/Downloads/TCLMod
Put these files inside:
autobooter_mac.py
system_patched_sparse.img
vbmeta
vbmeta_system
vbmeta_vendor
boot
recovery
dtbo
super_stock_sparse.img
super_stock_sparse.img is for recovery if something goes wrong.
1
Do not flash random images from another firmware version.
1. Install Homebrew
Install Homebrew from:
https://brew.sh
Then install Android platform tools:
brew install android-platform-tools
2. Install Python Serial Dependency
Run:
python3 -m pip install pyserial
If that gives a Python environment error, use this instead:
cd ~/Downloads/TCLMod
python3 -m venv venv
source venv/bin/activate
pip install pyserial
3. Boot the Phone into Regular Fastboot
Go to the mod folder:
cd ~/Downloads/TCLMod
Run the autobooter:
2
python3 autobooter_mac.py
If using the virtual environment:
source venv/bin/activate
python autobooter_mac.py
Then:
1.
2.
3.
4.
Turn the phone completely off.
Leave the script running.
Plug the phone into the Mac.
The script should say something like:
Waiting for MediaTek device...
Success!
The phone should show:
Fastboot mode
Confirm fastboot sees it:
fastboot devices
4. Unlock the Bootloader
Run:
fastboot flashing unlock
On the phone, press Volume Up to confirm.
This wipes the phone.
After unlock, return to regular fastboot if needed using the autobooter again.
3
5. Disable AVB / dm-verity Verification
From the TCLMod folder, run:
fastboot --disable-verity --disable-verification flash vbmeta vbmeta
fastboot --disable-verity --disable-verification flash vbmeta_system
vbmeta_system
fastboot --disable-verity --disable-verification flash vbmeta_vendor
vbmeta_vendor
This is required. If verification is not disabled, the phone will bootloop with a dm-verity corruption warning
after flashing the patched system.
6. Clear Metadata / Userdata
Run:
fastboot erase metadata
fastboot erase cache
fastboot -w
This is important. On KEKA, stale metadata can keep causing the corrupt-screen bootloop even after
flashing correct images.
7. Enter Fastbootd / Userspace Fastboot
Regular fastboot cannot safely flash the logical system partition. You need fastbootd.
Run:
fastboot reboot fastboot
Wait for the phone to reconnect.
Check:
4
fastboot getvar is-userspace
You want:
is-userspace: yes
If it does not say yes , do not flash system.
8. Flash the Patched System Image
Make sure system_patched_sparse.img is in the folder.
Run:
fastboot -S 100M flash system system_patched_sparse.img
Then reboot:
fastboot reboot
The phone may show a warning that the device is corrupt or cannot be trusted. Press Power or the phone’s
confirm/OK key to continue.
If it bootloops or will not continue, return to regular fastboot and run:
fastboot erase metadata
fastboot erase cache
fastboot -w
fastboot reboot
9. Finish Android Setup
Let the phone boot fully.
Go through the normal setup screen.
5
It may say something about switching carrier or provisioning. Let it finish.
10. Install Aurora Store
Do not open APK files from File Manager. File Manager may still say APK installation is not available.
Use the browser download notification method:
1.
2.
Open the phone browser.
Go to:
https://f-droid.org/en/packages/com.aurora.store/
1.
2.
3.
4.
5.
Scroll to the APK download.
Download the APK.
Pull down the notification shade / notification tab.
Tap the completed download notification.
Install Aurora Store.
After Aurora installs, use it to download apps.
Recovery If the Phone Bootloops
Get back into regular fastboot using the autobooter.
From the TCLMod folder, restore stock super :
fastboot -S 100M flash super super_stock_sparse.img
Then flash stock boot-chain images:
fastboot flash boot boot
fastboot flash recovery recovery
fastboot flash dtbo dtbo
Then disable verification again:
fastboot --disable-verity --disable-verification flash vbmeta vbmeta
fastboot --disable-verity --disable-verification flash vbmeta_system
6
vbmeta_system
vbmeta_vendor
fastboot --disable-verity --disable-verification flash vbmeta_vendor
Then wipe metadata and userdata:
fastboot erase metadata
fastboot erase cache
fastboot -w
fastboot reboot
Do not interrupt a super flash once it starts.
Do not flash:
preloader
lk
lk2
tee
gz
nvram
proinfo
unless you know exactly what you are doing.
Notes
The old KEFH method relied on enabling ADB and then installing APKs through ADB.
KEKA blocks that older path.
This KEKA method works differently:
Patch services.jar
Flash patched system
Disable AVB / dm-verity
Clear metadata
Boot normally
Install APKs from browser download notification
Root is not required.
7
ADBio is not required.
Magisk is not required.
Aurora Store works after the patched system is flashed.
File Manager may still block APK files, so use the browser download notification path.
8
