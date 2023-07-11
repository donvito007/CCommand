#AdbCommand
Collection of various practical commands of Adb

If you connect multiple devices, use adb -s to operate.
Use findstr under windows, and grep under linux and mac.

Connect to ADB via wireless:
- Plug in the phone and enter the command: adb tcpip 5555
- Enter the connection command: adb connect 172.16.7.204:5555

Execute shell commands as root:
- Execute adb root first

When `Read-only file system` is executed as readable and writable:
- adb remount


If the result is too long, it can be saved to a file, such as:
- `>` New or overwrite file save: `adb shell pm list packages > installed_package.txt`
- `>>`appends the result to the file: `adb shell pm list packages >> installed_package.txt`
 

## Device connection operation:
### adb shutdown:
adb kill-server

### adb open:
adb start-server

### adb device view:
adb devices

### adb view mobile network
adb shell ifconfig wlan0

### Upload files from computer to phone:
adb push /Users/caochang/apk/BBox.apk /sdcard/xbd/BBox.apk

### Send files from phone to computer:
adb pull /sdcard/xbd/BBox.apk /Users/caochang/apk/BBox.apk


## Get system parameters:
### Output device information to a file:
adb shell getprop > info.txt

### Get IMEI:
adb shell dumpsys iphonesubinfo

### Get the phone brand:
adb shell getprop ro.product.brand

### Get the phone model:
adb shell getprop ro.product.model

### Get the SDK version number of the mobile phone system:
adb shell getprop ro.build.version.sdk

### Get the Android system version number:
adb shell getprop ro.build.version.release

### Get phone resolution:
adb shell wm size

### Modify the phone resolution:
adb shell wm size 1080*1920

### Get the phone dpi:
adb shell wm density

### Modify the phone dpi:
adb shell wm density 480

### Get the mobile phone cpu architecture:
adb shell getprop ro.product.cpu.abilist (after Android5.0 system)
adb shell getprop ro.product.cpu.abi (before Android5.0 system)

### Get the phone mac:
adb shell cat /sys/class/net/eth0/address (network card one)
adb shell cat /sys/class/net/wlan0/address (wireless network card)

### Get deviceId:
adb shell settings get secure android_id
adb shell content query --uri content://settings/secure/android_id --projection value

### View the codecs supported by the system:
adb shell system /etc/media_codecs.xml
adb shell cat /etc/media_codecs.xml | grep -i "hevc" (check if h265 is supported)


## System settings related:
### Get the screen sleep time:
adb shell settings get system screen_off_timeout

### Set the screen sleep time (ms):
adb shell settings put system screen_off_timeout 60000

### View the default SMS application:
adb shell settings get secure sms_default_application

### Modify the default SMS application:
adb shell settings put secure sms_default_application com.carlos.sms

### Get the screen off status:
adb shell "dumpsys window policy | grep mScreenOnFully"
<br>
adb shell dumpsys power | findstr "Display Power:state="

### View system properties
adb shell cat /system/build.prop
`heapgrowthlimit`: Common application memory limit, corresponding to the value obtained by the `ActivityManager.getMemoryClass()` method
`heapsize`: After `largeHeap=true` is set in `manifest`, the maximum memory value that can be used, corresponding to the value obtained by `ActivityManager.getLargeMemoryClass()` method

### View system virtual machine type
adb shell getprop persist.sys.dalvik.vm.lib
If the value is libdvm.so, it is dalvik, and libart.so is art

### View and set related properties
Under the Android4.4 directory, the relevant path `data/data/com.android.providers.settings/databases/settings.db`, you can open the database to view
Under the Android7.x directory, the relevant path `data/system/users/0`, `settings_secure.xml`, `settings_system.xml`, `settings_global.xml` files under the path
get value
```
adb shell settings get system [key]
adb shell settings get global [key]
adb shell settings get secure [key]
```
Settings
```
adb shell settings put secure [key] [value]
...
```
For example, get the default duration (in milliseconds) before the defined press turns into a long press: `adb shell settings get secure long_press_timeout`


## Performance related:
### Time-consuming hot and cold start:
```
adb shell am start
     -W: wait for launch to complete
     -S: force stop the target app before starting the activity
```
Example `adb shell am start -W com.UCMobile/com.uc.browser.InnerUCMobile`
Filterable startup log for `displayed` output in AS

### Application cpu usage:
adb shell dumpsys cpuinfo | find "com.sec.android.app.launcher"

### Application memory:
adb shell "dumpsys meminfo | grep com.carlos.bbox

### Get pid by package name:
adb shell "ps | grep com.aspire.agent"
The value of the second column of the result is pid
### Get uid by pid:
adb shell cat /proc/6094/status

### Obtain uplink traffic through uid:
adb shell "cat /proc/uid_stat/10189/tcp_snd"

### Obtain downlink traffic through uid:
adb shell "cat /proc/uid_stat/11110/tcp_rcv"

### Export crash log:
adb shell dumpsys dropbox --print >>crashlog_$(date +%Y%m%d%H%M).txt

### View meminfo content
adb shell cat /proc/meminfo

### Simulate killing process by pid
adb shell kill -9 6094

## Application related:
### Show installed apps:
adb shell pm list packages [-com.carlos.bbox]
### Clear application data:
adb shell pm clear com.carlos.bbox
### Uninstall the app:
adb uninstall com.carlos.bbox
### Uninstall system apps
adb shell pm uninstall -k --user 0 com.carlos.test
### Explicitly install the application:
adb install (-r force installation) /Users/caochang/apk/BBox.apk
### Install apps implicitly:
adb shell pm install (-r) /sdcard/xbd/BBox.apk
### Install the application without pop-up box:
adb install-multiple -r /Users/caochang/apk/BBox.apk
### Send broadcast:
adb shell am broadcast -a com.carlos.bbox -e port 8888
### Start the service:
adb shell am startservice com.carlos.bbox/com.carlos.bbox.MyService
### Start the application:
adb shell am start -n com.carlos.bbox/.MainActivity
Start with parameters:
```
-a action; the action corresponding to the activity;
--es key stringValue; pass String parameter;
--ez key booleanValue; pass Boolean parameter;
--ei key intValue; pass int parameter;
--el key longValue; pass long parameter;
--ef key floatValue; pass float parameter;
```

### Stop the application:
adb shell am froce-stop com.carlos.bbox
### View the top-level Activity class name:
- Commands below 8.0
adb shell dumpsys activity | grep "mFocusedActivity"
- Commands above 8.0
adb shell dumpsys activity | grep "mResumedActivity"
### View the information of the current interface
adb shell dumpsys window | grep mCurrentFocus

### Activity information and View Hierarchy:
adb shell dumpsys activity top
### Check if the app is installed
adb sehll pm list packages | grep "com.carlos.test"
### View the a corresponding to the installed package name
