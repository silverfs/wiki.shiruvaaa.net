!!! warning
	I don't know why, but this guide only works with Linux. Windows will not work with this device!


<br>

# How to Re-Root after update


## Prerequisites

Make sure you have the following installed on your computer:
- adb
- python =< 3

Make sure your phone already has the Magisk application. Also, make sure you have installed the update and rebooted the device using the default method before you follow the steps below.

<br>

---

## Steps

1. Download [LineageOS/scripts](https://github.com/LineageOS/scripts) off of Github.
2. Install the [latest update](https://download.lineageos.org/fajita) of LineageOS.
3. Extract `payload.bin` from the .zip and move it to the root directory of the downloaded repo (LineageOS/scripts).
4. In that root directory, execute the following command:

```bash
python ./update-payload-extractor/extract.py payload.bin --output_dir ./output
```

!!! warning
	**1.** If you receive the error which tells you that the module 'google' was not found, simply install it using `pip install --upgrade google-api-python-client`.
	**2.** If you receive the error 'Descriptors cannot not be created directly.', downgrade protobuf using `pip install protobuf==3.20.0`.


<br>

5. Navigate to the `output` folder. 

6. Push your `boot.img` to the device:

```bash
adb push ./boot.img /sdcard
``` 

7. Magisk: patch from file. Select `boot.img`.

8. After it's done, pull your patched image and flash the magisk_patched file:

```
adb pull /sdcard/Download/magisk_patched-25200-<SOME-STRING-HERE>.img
```

```
adb reboot fastboot
```

```
fastboot flash --slot=all boot magisk_patched-<SOME-STRING-HERE>.img
```

9. Finally, reboot the device through the Device's screen. 

<br>
<br>

## Uncategorized links:

- https://github.com/topjohnwu/Magisk/issues/5299
- https://wiki.lineageos.org/devices/fajita/install
- https://download.lineageos.org/fajita
- https://topjohnwu.github.io/Magisk/install.html#patching-images

