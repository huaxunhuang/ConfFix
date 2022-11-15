# ConfFix

This repository contains the ConfFix tool, the evaluation detail and the benchmark of real-world configuration compatibility issues, which are available in three separate folders: empirical dataset, source code, evaluation.

# ConfFix Dataset

The dataset for evaluating ConfFix contains 81 reproducible CC issues. Please check ```./evaluation/issue_in_the_latest_version.xlsx``` and ```./evaluation/issue_in_the_past.xlsx``` for the information of 81 CC issues, including the reproduction videos and our submitted pull requests and **issue reports** to the app developers. **We submitted patches for 39 successfully-repaired issues, of which 38 have been confirmed and merged by the app developers.** For the detail please check ```./evaluation/issue_in_the_latest_version.xlsx```.

The APK files and test cases of these 81 CC issues are provided in conffix_subjects.zip, which you can find in this project release due to the maximum size limit of GitHub. We provide  ```.sh``` file for each issue to help you reproduce the results of ConfFix.

# Build and Use ConfFix from Scratch

## Prerequisite

* UIAutomator2
* Python 3
* Android emulators at target API levels (e.g., API level 33)

## Steps

### 1. Create emulators
1. Download Android SDK Command line tools [link](https://dl.google.com/android/repository/commandlinetools-linux-7302050_latest.zip)

Rename cmdline-tools to be tools and put it under an SDK root folder (e.g., ```/ssddata/yourname/SDK/android_sdk_linux```). So now the tools path will be ```/ssddata/yourname/SDK/android_sdk_linux/tools```

2. Rename cmdline-tools to be tools and put it under an SDK root folder (e.g., ```/ssddata/yourname/SDK/android_sdk_linux```). So now the tools path will be ```/ssddata/yourname/SDK/android_sdk_linux/tools```

3. Navigate to the SDK root and type in the following commands to install necessary files for the SDK
```
(1) tools/bin/sdkmanager --sdk_root=/ssddata/yourname/SDK/android_sdk_linux --install "system-images;android-33;google_apis;x86_64"
(2) tools/bin/sdkmanager --sdk_root=/ssddata/yourname/SDK/android_sdk_linux --install "platforms;android-33"
(3) tools/bin/sdkmanager --sdk_root=/ssddata/yourname/SDK/android_sdk_linux --install "platform-tools"
(4) tools/bin/sdkmanager --sdk_root=/ssddata/yourname/SDK/android_sdk_linux --install "emulator"
```
4. Add something into PATH

```
export ANDROID_HOME=/ssddata/yourname/SDK/android_sdk_linux
export PATH=$PATH:$ANDROID_HOME:$ANDROID_HOME/emulator:$ANDROID_HOME/tools/bin:$ANDROID_HOME/platform-tools
export ANDROID_SDK_ROOT=$ANDROID_HOME
export ANDROID_AVD_HOME=~/.android/avd
export PATH=$PATH:$ANDROID_AVD_HOME
```

5. Create an folder under the SDK root named avds and create an Android emulator: 
```avdmanager create avd -n emulator0 -k "system-images;android-33;google_apis;x86_64" -p avds/emulator0```

To run ConfFix, please also create another emulator for the issue-inducing API level, as the following example.
```avdmanager create avd -n emulator0 -k "system-images;android-22;google_apis;x86_64" -p avds/emulator1```

6. Modify the emulator configuration to change the screen size of your emulator:
In our evaluation, we set an emulator with 560dpi density. The density is set following the device profile of Google Pixel 6 Pro.
To achieve this, modify the emulator configuration files (the file usually is: ```./emulator0/config.ini```).
Please change ```hw.lcd.density=560```

7. Launch the emulators: 
```emulator @emulator0 -wipe-data -no-window -no-audio -memory 4096 -port 5554 -partition-size 8192 -skin 1440x3120```
```emulator @emulator1 -wipe-data -no-window -no-audio -memory 4096 -port 5556 -partition-size 8192 -skin 1440x3120```
This command launches the emulator ```emulator0``` and ```emulator1``` in the port 5554 and 5556. We set the device with 4GB RAM, 8GB disk, and 1440x3120 resolution. The resolution is set following the device profile of Google Pixel 6 Pro.


### 2. Run ConfFix

1. Install uiautomator2, which is used for executing test scripts
```
pip3 install --upgrade --pre uiautomator2
```

2. Add zipaligner and apksigner, which are used to sign the apk files generated by apktool. You can find zipaligner and apksigner in ```android_sdk/build-tools/31.0.0``` (the version number of the build tools you installed). Add the following environment variable.

```
export BUILD_TOOL_ROOT=$ANDROID_SDK_ROOT/build-tools/31.0.0
```

3. To reproduce the experiment result, we have prepared a ```.sh``` file for each issue to run. For example, to try cwa_tan_input_digit_error, use the following command.
```
python -u xmlutilrandom_uiautomator.py emulator-5554 emulator-5556 path-to-the-subject-apks/Corona-Warn-App-tan_input_digit.apk res/drawable/tan_input_digit_error.xml item gravity0height dimension de.rki.coronawarnapp tan_input_digit_error cwa__tan_input_digit_error
```
ConfFix generates a patch as follows
<img width="80" alt="image" src="https://user-images.githubusercontent.com/109571086/201920092-da66cd8e-8475-47d2-b148-06d5cb31e525.png">


For the test scripts written by Espresso, the command to run ConfFix is as follows.
```
python -u xmlutilrandom_testcase.py emulator-5554 emulator-5556 path-to-the-subject-apks/FairEmail-tvPrivacy.apk res/layout-v22/fragment_setup.xml TextView drawableTint color path-to-the-espresso-test-apks/FairEmail-androidTest.apk ConfFixTest#tvTutorials eu.faircode.email.debug.test eu.faircode.email.debug tvTutorials
```
