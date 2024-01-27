!!! warning
	Using `adb` on Windows doesn't seem to work with this guide. Please use Linux (or WSL - not tested) to patch and re-root your device.


<br>

# How to Re-Root after update

This guide is intended for a rooted Oneplus 6T (fajita) running LineageOS 19/20 with Magisk.

## Prerequisites

Make sure you have the following installed on your computer:
- [adb](https://developer.android.com/tools/adb)
- [Python](https://www.python.org/) =< 3

Make sure your phone already has the Magisk application. Also, make sure you have installed the update and rebooted the device using the default method before you follow the steps below. If Magisk has an update on its 
application, don't update just yet and read the steps below first.

It is highly advised to read everything carefully before going along with the steps.

!!! info
	There is new repo by LineageOS called [android_tools_extract-utils](https://github.com/LineageOS/android_tools_extract-utils), and is here to replace the `update-payload-extractor` in the older script due to some licensing stuff described in [this commit](https://github.com/LineageOS/scripts/commit/09efdf2c18180cc91f74659bf864a20b241f88aa). I have not tested this out yet, so as long as these steps still work without a problem, I won't update this page yet.
<br>

---

## Steps

1. Download this specific commit of [LineageOS/scripts](https://github.com/LineageOS/scripts/tree/dd225e1cebc81f693ac1b981ac853cf819321b49) from GitHub which has the update-payload-extractor script.
2. Download the [latest update](https://download.lineageos.org/fajita) of LineageOS and store it somewhere on your PC.
3. Extract `payload.bin` from the LineageOS update .zip and move it to the root directory of the downloaded repo (LineageOS/scripts).
4. In that root directory, execute the following command:

```bash
python3 ./update-payload-extractor/extract.py payload.bin --output_dir ./output
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
adb pull /sdcard/Download/magisk_patched-<SOME-STRING-HERE>.img
```

!!! info
	If the Magisk application on your Oneplus 6T needs an update, this is the part where you can update it safely. Do so *before* booting into fastboot.

```
adb reboot fastboot
```

```
fastboot flash --slot=all boot magisk_patched-<SOME-STRING-HERE>.img
```

9. Finally, reboot the device through the Device's screen. 
10. Check the Magisk app on full root coverage and double-check if everything else works as well.

<br>

That should be it for now.
Happy tinkering ✨
<br>

## Uncategorized links:

- https://github.com/topjohnwu/Magisk/issues/5299
- https://wiki.lineageos.org/devices/fajita/install
- https://download.lineageos.org/fajita
- https://topjohnwu.github.io/Magisk/install.html#patching-images

