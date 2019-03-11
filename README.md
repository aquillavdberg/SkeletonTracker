# SkeletonTracker
An openFrameworks app that tracks and broadcasts skeletal data from a  Microsoft Kinect (V2)

- [Basic Usage and Limitations]()
- [Getting Started]()
    - [Building From Scratch]()
    - [Setting Up the Project]()
- [Running the Exe]()


## Basic Use and Limitations
Tested with: 
- MSVS Pro 2017
- [openFrameworks v0.9.8](https://openframeworks.cc/download/older/)
- [Kinect SDK 2.0](https://www.microsoft.com/en-us/download/details.aspx?id=44561)
- [Google Protobuf 3](https://developers.google.com/protocol-buffers/)

Required openframeworks addons:
- [ofxKinectForWindows2](https://github.com/elliotwoods/ofxKinectForWindows2)
- [ofxOneEuroFilter](https://github.com/i-n-g-o/ofxOneEuroFilter)
- ofxNetwork
- ofxGui

#### Basic Controls and Usage

#### Limitations

Out of the box, this app tracks many skeletons, but will only broadcasting skeletal data of one person.

## Getting Started

### Building From Scratch
Follow these instructions when setting up on a new machine:

1. Download and Install Microsoft Visual Studios 2017.

2. Download and Install the [Kinect SDK 2.0](https://www.microsoft.com/en-us/download/details.aspx?id=44561) from Microsoft.

3. Restart your computer to finalize the MSVS and Kinect SDK setup.

    1. Verify successful installation by plugging your sensor into a USB 3.0 port and running the SDK's `Kinect Studio v2.0` app. Under the `MONITOR` tab, click the icon at the top left to toggle _Connected / Not Connected_.

    ![](https://github.com/madelinegannon/SkeletonTracker/blob/master/reference/skeleton-tracker_kinect-sdk.PNG)

5. [Download](https://openframeworks.cc/download/older/), [Install](https://openframeworks.cc/setup/vs/), and [Build](https://openframeworks.cc/learning/01_basics/how_to_add_addon_to_project/) **openFrameworks v0.9.8**.

6. Clone the required addons in your `OF_PATH/addons` folder.

    1. Each of the addons should have a README if there are more specific installation instructions.

    2. Note that for [ofxKinectForWindows2](https://github.com/elliotwoods/ofxKinectForWindows2), you need to checkout the `0.9.0` tag: 

    ```
    > cd OF_PATH/addons/ofxKinectForWindows2
    > git checkout 0.9.0
    ```

7. Download and Install Google Protobuf:

    Start by [installing vcpkg](https://github.com/Microsoft/vcpkg):
    
    ```
    > cd /
    > git clone https://github.com/Microsoft/vcpkg.git
    > cd vcpkg

    PS> .\bootstrap-vcpkg.bat
    PS> .\vcpkg integrate install
    ```
    
    If `vcpkg` and protobuf are already installed, you can update to the latest protobuf compilier by doing [this](https://github.com/Microsoft/vcpkg/blob/master/docs/about/faq.md#how-do-i-update-libraries).

    
    Follow Protobuf C++ Installation Instructions [for Windows](https://github.com/protocolbuffers/protobuf/blob/master/src/README.md#c-installation---windows):
    
    ```
    PS> vcpkg install protobuf protobuf:x64-windows
    ```

    Add a SYSTEM VARIABLE for your vcpkg directory path:

    - Search for _Advanced System Settings_
    - Click the _Environmental Variables_ button on the bottom left
    - Create a new System Variable `VCPKG` for `/path/to/vcpkg`
    
8. Build the `body.proto`:

    Navigate to `C:\vcpkg\installed\protobuf\x64-windows\tools\protobuf` and make a new directory named `body`:
        
    ```
    cd vcpkg/installed/protobuf/x64-windows/tools/protobuf
    mkdir body
    ```
       
    Copy the `\proto\body.proto` in this repo to our new `\body` directory:
        
    ```
    cp ~/path/to/repo/proto/body.proto ./body/
    ```

    Generate the C++ or Python proto files: 

    `For C++:`
    ```
    .\protoc --proto_path=body --cpp_out=body body/body.proto
    ```
        
    `For Python:`
    ```
    .\protoc --proto_path=body --python_out=body body/body.proto
     ```
 
### Setting Up the Project
Once you have the basic components installed on your computer, follow these instructions to setup the MSVS project:

1. Open the openFrameworks Project Generator and click `import` to load the existing project directory.

   You should see the following addons selected:
       
   ```
   ofxGui
   ofxNetwork
   ofxOneEuroFilter
   ofxOsc
   ```

   **DO NOT** add ofxKinectForWindows2 yet ... we'll do that next manually.

   Hit `Update` and `Open in IDE` to bring up the MSVS solution.

2. Add `ofxKinectForWindows2` project dependency to your solution.
    
    Follow the _Usage_ instructions from the [ofxKinectForWindows2 README](https://github.com/elliotwoods/ofxKinectForWindows2) to properly add the addon:

    - Open the solution, and add the ofxKinectForWindows2Lib.vcxproj to your solution (right click on the Solution and choose _Add > Existing Project..._)

    - In Property Manager (open it from _View -> Other Windows -> Property Manager_), right click on your project to select _Add Existing Property Sheet..._ and select the `ofxKinectForWindows2.props` file.

    - Go back to Solution Explorer, right click on your project (e.g. 'mySketch') and select '_Add Reference..._', and add a reference to `ofxKinectForWindows2Lib`.

    - In _Project Properties > C/C++ > General > Additional Include Directories_, add the following:

    ```
    $(KINECTSDK20_DIR)\inc;..\..\..\addons\ofxKinectForWindows2\src
    ```

3. Add `protobuf` to the solution.

    ~~In Property Manager (open it from _View -> Other Windows -> Property Manager_), right click on your project to select _Add Existing Property Sheet..._ and select the `SkeletalTracker.props` file.~~ ... Not working properly yet :(

    In your project's _Project Properties > C/C++ > General > Additional Include Directories_, add the following: 

    ```
    $(VCPKG)\installed\x64-windows\tools\protobuf\body;$(VCPKG)\installed\x64-windows\include;$(VCPKG)\installed\x64-windows\include\google;$(VCPKG)\installed\x64-windows\include\google\protobuf;$(VCPKG)\installed\x64-windows\include\google\protobuf\compiler;$(VCPKG)\installed\x64-windows\include\google\protobuf\compiler\cpp;$(VCPKG)\installed\x64-windows\include\google\protobuf\io;$(VCPKG)\installed\x64-windows\include\google\protobuf\stubs;$(VCPKG)\installed\x64-windows\include\google\protobuf\util
    ```

    In _Project Properties > C/C++ > Linker > Input > Additional Dependencies_ add the following:

    ```
    $(VCPKG)/installed/x64-windows/lib/libprotobuf.lib
    ```

    In the Solution Explorer, right click on the project and select '_Add Existing Item..._'. 

    - Add the `body.pb.cc` and `body.pb.h` files from the `C:\vcpkg\installed\protobuf\x64-windows\tools\protobuf\body` directory.

    Copy _libprotobuf.dll_ from `$(VCPKG)/installed/protobuf/x64-windows/bin` to the project directory that containing the .exe:

    ```
    > cp ~/vcpkg/installed/x64-windows/tools/protobuf/libprotobuf.dll ~/path/to/repo/SkeletonTracker/bin
    ```

4. At this point, you should see no linker or include errors in your MSVS solution. If that's not the case, double check your Project Properties file names.


## Running the App

### Calibrate the Sensor

Set up your Kinect on a tripod or stable surface about 1 to 1.5 meters away from your desired Interaction Zone.

This app uses a sophisticated and highly-technical calibration routine to ensure the sensor is properly mapped to a robot's coordinate system: in the app, you use the GUI and the _Sensor_Params_ sliders to roughly adjust the {X, Y, Z} and Tilt directions of the sensor. 

![](https://github.com/madelinegannon/SkeletonTracker/blob/master/reference/skeleton-tracker_params_sensor-offset.gif)

These values are saved on exit in the _bin/data/settings.xml_ file. You can manually adjust the values in this file, and then restart the app for finer numeric control.

### Using the GUI

There are a few useful things to point out in the GUI. First, hitting the `Save` icon in the top right writes out the current parameters to a settings file in  _bin/data/settings.xml_ file.

Second, you can toggle streaming data ON and OFF using `Stream Bodies` button towards the top.

![](https://github.com/madelinegannon/SkeletonTracker/blob/master/reference/skeleton-tracker_params_streaming.png)

#### Keypressed Cheat Sheet

`G`:
Toggles hiding the GUI and Fullscreen Mode

`1`:
Show TOP VIEW

`2`:
Show FRONT VIEW

`3`:
Show SIDE VIEW

`4`:
Show PERSPECTIVE VIEW