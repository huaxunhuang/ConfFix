# ConfFix

This repository contains the ConfFix tool, the evaluation detail and the benchmark of real-world configuration compatibility issues.

# ConfFix Dataset

Please find ```Section_3.xlsx``` for the detailed information of our empirical study in Section 3. 

The dataset for evaluating ConfFix contains 78 reproducible CC issues. Please check ```./evaluation/issue_in_the_latest_version.xlsx``` and ```./evaluation/issue_in_the_past.xlsx``` for the information of 81 CC issues, including the reproduction videos and our submitted pull requests and **issue reports** to the app developers. **We submitted patches for 39 successfully-repaired issues, of which 38 have been confirmed and merged by the app developers.** For the detail please check ```./evaluation/issue_in_the_latest_version.xlsx```.

The APK files and test cases of these 78 CC issues are provided [here](https://1drv.ms/u/s!AnUUjcFFYAJEbPxHcm9MlkrX8zw?e=twSegT). Please put them inside the ```./source_code``` folder when you run the experiment.

We provide a  ```.sh``` file for each issue to help you reproduce the results of ConfFix (```conffix-exp-sh.rar```) and XFix (```xfix-exp-sh.rar```) . Put them in the root directory of ```./source_code``` to make them a try.

We also release the experimental results of ConfFix, XFix and Lint in ```./evaluation/Lint&ConfFix&XFix output.xlsx```. Besides, the raw outputs of ConfFix and XFix can be found in ```./evaluation/raw_outputs/``` folders.

# Build and Use ConfFix from Scratch

## Prerequisite

* UIAutomator2
* Python 3
* Android emulators at target API levels (We use API level 33 when conducting experiments)

## Get Started

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
(5) tools/bin/sdkmanager --sdk_root=/ssddata/yourname/SDK/android_sdk_linux --install "build-tools;31.0.0"
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

6. Launch the emulators: 

```emulator @emulator0 -wipe-data -no-window -no-audio -memory 4096 -port 5554 -partition-size 8192 -skin 1440x3120```

```emulator @emulator1 -wipe-data -no-window -no-audio -memory 4096 -port 5556 -partition-size 8192 -skin 1440x3120```

This command launches the emulator ```emulator0``` and ```emulator1``` in the port 5554 and 5556. We set the device with 4GB RAM, 8GB disk, and 1440x3120 resolution. Note that it is possible to launch multiple emulators at the same time to boost your experiments.


### 2. Validate experiement results of ConfFix and XFix

1. Install uiautomator2, which is used for executing test scripts
```
pip3 install --upgrade --pre uiautomator2
```

2. Add zipaligner and apksigner, which are used to sign the apk files generated by apktool. You can find zipaligner and apksigner in ```android_sdk/build-tools/[version_number]``` (the version number of the build tools you installed). Make sure you have added the following environmental variables

```
export ANDROIDPLATFORMPATH=$ANDROID_HOME/platform-tools
export ANDROIDBUILDPATH=$ANDROID_HOME/build-tools/31.0.0
```

3. To reproduce the experiment result, we have prepared a ```.sh``` file for each issue to run (```./source_code/conffix-exp-sh.zip``` and ```./source_code/xfix-exp-sh.zip```). For example, to try cwa_tan_input_digit_error, use the following command.
```
sh exp_cwa_tan_input_digit_error.sh emulator-5554 emulator-5556
```
where ```emulator-5554``` stands for the devices in the target API level, and ```emulator-5556``` stands for the devices in the issue-inducing API level.

For each of 78 issues, we provide the raw outputs of ConfFix and XFix in ``./evaluation/raw_outputs`` to validate the results.

ConfFix generates a patch as follows.
![conffix-demo](https://user-images.githubusercontent.com/109571086/201925686-3bf70abe-f62d-46b4-a929-6885a4d2f76c.png)

