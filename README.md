
# AI Hosital Bed Occupency Monitoring With Edge Impulse & Arduino Nano 33 BLE Sense
![AI Hosital Bed Occupency Monitoring With Edge Impulse & Arduino Nano 33 BLE Sense](assets/images/ai-hospital-bed-tracking.jpg "AI Hosital Bed Occupency Monitoring With Edge Impulse & Arduino Nano 33 BLE Sense")

Project by [Adam Milton-Barker](https://www.AdamMiltonBarker.com)

# Introduction

Hospitals can benefit greatly from the use of modern technologies and automation. As hospitals continue to struggle through lack of staff, they need to explore ways that tasks can be automated to free up their valuable human resources. AI is one technology that can play a huge role in the automation of hospitals, and with platforms such as Edge Impulse and low cost embedded devices, automation can be implemented easily, and at low cost. 

# Solution

Using the built in sensors of the Arduino Nano 33 BLE SENSE and the Edge Impulse platform, beds can be monitored to see if they are occupied or not, meaning that hospital staff can know in real-time whether or not they have room for a patient, and exactly where the free beds are. This project is a proof of concept to show how Edge Impulse can be used to train a custom neural network, which can be deployed to an Arduino Nano 33 BLE SENSE.  

## Hardware

- Arduino Nano 33 BLE Sense [Buy](https://store.arduino.cc/products/arduino-nano-33-ble-sense)

## Platform

-  Edge Impulse [Visit](https://www.edgeimpulse.com)

## Software

- [Edge Impulse CLI](https://docs.edgeimpulse.com/docs/edge-impulse-cli/cli-installation)
- [Arduino CLI](https://arduino.github.io/arduino-cli/latest/)
- [Arduino IDE](https://www.arduino.cc/en/software)

## Project Setup

Head over to [Edge Impulse](https://www.edgeimpulse.com) and create your account or login. Once logged in you will be taken to the project selection/creation page.

### Create New Project
Your first step is to create a new project. From the project selection/creation you can create a new project.

![Create Edge Impulse project](assets/images/1-Create-Project.jpg "Create Edge Impulse project")

Enter a **project name**, select **Developer** or **Enterprise** and click **Create new project**.

![Choose project type](assets/images/2-Choose-project-type.jpg "Choose project type")

We are going to be creating a project that uses the built in accelerometer, gyroscope and magnetometer sensors, so now we need to select **Accelerometer Data** as the project type.

### Connect Your Device

![Connect device](assets/images/3-Connect-development-board.jpg "Connect device")

You need to install the required dependencies that will allow you to connect your device to the Edge Impulse platform.

![Connect device](assets/images/4-Connect-Arduino-Nano-33-BLE-Sense.jpg "Connect device") 

This process is documented on the [Edge Impulse Website](https://docs.edgeimpulse.com/docs/development-platforms/officially-supported-mcu-targets/arduino-nano-33-ble-sense) and includes installing:

- [Edge Impulse CLI](https://docs.edgeimpulse.com/docs/edge-impulse-cli/cli-installation)
- [Arduino CLI](https://arduino.github.io/arduino-cli/latest/)

Once the dependencies are installed, connect your device to your computer and press the **RESET** button twice to enter into bootloader mode, the yellow LED should now be pulsating.

Now download the the [latest Edge Impulse firmware](https://cdn.edgeimpulse.com/firmware/arduino-nano-33-ble-sense.zip) and unzip it, then double click on the relevant script for your OS either `flash_windows.bat`, `flash_mac.command` or `flash_linux.sh`.

Once the firmware has been flashed you will should see the output above, hit enter to close command prompt/terminal.

Open a new command prompt/terminal, and enter the following command:

```edge-impulse-daemon```

If you are already connected to an Edge Impulse project, use the following command:

```edge-impulse-daemon --clean```

Follow the instructions to log in to your Edge Impulse account.

![Device connected to Edge Impulse](assets/images/5-Arduino-connected.jpg "Device connected to Edge Impulse")

Once complete head over to the devices tab of your project and you should see the connected device.

## Data Acquisition

We are going to create our own dataset, using the built in sensors on the Arduino Nano 33 BLE Sense. We are going to collect data that will allow us to train a machine learning model that can detect sitting down on a bed, standing up, and idle state.

We will use the **Record new data** feature on Edge Impulse to record around 35 samples of each class.

![Vacant data](assets/images/6-Collect-data-vacant.jpg "Vacant data")

Connect your Arduino Nano 33 BLE SENSE to the Edge Impulse platform, and connect it to the side of your bed. Once you have this in place, set the label to **Vacant** and record yourself standing up from the bed around 35 times.

![Occupied data](assets/images/7-Collect-data-occupied.jpg "Occupied data")

Now you need to do the same for sitting down on the bed, repeat the steps above and change the label to **Occupied** before recording your samples.

Finally, record the **Idle** data, change the sample length to 1000 (1 second), and record your data, ensuring no movement is detected during sampling.

### Crop Data

![Crop data](assets/images/8-Crop-data.jpg "Crop data")

As the samples we took for the **Vacant** and **Occupied** classes were taken with a sample length of 5 seconds, we will need to trim them down to around 1 second. 

To do this, click the three dots at the side of the samples and reduce the length by pulling bars at the edges of the samples closer together. Make sure you get the correct data in the sample. For Vacant, you should have the part of the sample where the signals go up, and vice-a-versa for the Occupied samples.

### Test Split

![Test Split](assets/images/14-Impulse-test-split.jpg "Test Split")

We now need to split the data into training and test data. To do so head to the project dashboard and scroll down to the bottom. Click on the **Perform train/test split** button and follow the instructions.

## Create Impulse

![Create Impulse](assets/images/10-Create-impulse.jpg "Create Impulse")

Now we are going to create our network and train our model. Head to the **Create Impulse** tab. Next click **Add processing block** and select **Spectral Analysis**.

Now click **Add learning block** and select **Classification**.

![Created Impulse](assets/images/11-Create-impulse-spectral-analysis-classifitcation.jpg "Created Impulse") 

Now click **Save impulse**.

### Spectral Features

#### Parameters

![Parameters](assets/images/12-Impulse-parameters.jpg "Parameters")

Head over to the **Spectral Features** tab and click on the **Save parameters** button to save the parameters.

#### Generate Features

![Generate Features](assets/images/13-Impulse-features.jpg "Generate Features")

If you are not automatically redirected to the **Generate features** tab, click on the **Spectral Features** tab and then click on **Generate features** and finally click on the **Generate features** button.

Your data should be nicely clustered and there should be as little mixing of the classes as possible. You should inspect the clusters and look for any data that is clustered incorrectly. If you find any data out of place, you can relabel or remove it. If you make any changes click **Generate features** again.

## Training

![Training](assets/images/15-Trained.jpg "Training")

Now we are going to train our model. Click on the **Classifier** tab then click **Start training**.

Once training has completed, you will see the results displayed at the bottom of the page. Here we see that we have 100% accuracy. Lets test our model and see how it works on our test data.

## Testing

### Platform Testing

Head over to the **Model testing** tab where you will see all of the unseen test data available. Click on the **Classify all** and sit back as we test our model.

![Test model results](assets/images/16-model-testing.jpg "Test model results")

You will see the output of the testing in the output window, and once testing is complete you will see the results. In our case we can see that we have achieved 100% accuracy on the unseen data.

### On Device Testing

Before we deploy the software to the Nano 33 BLE SENSE, lets test using the Edge Impulse platform whilst connected to the board. For this to work make sure your device is currently connected.

![Live testing: Idle](assets/images/16-live-testing-idle.jpg "Live testing: Idle")

![Live testing: Vacant](assets/images/16-live-testing-vacant.jpg "Live testing: Vacant")

![Live testing: Occupied](assets/images/16-model-testing.jpg "Live testing: Occupied")

Use the **Live classification** feature to record some samples for clasification from the Nano BLE SENSE. Your model should correctly identify the class for each sample.

## Deployment

Now we will deploy an Arduino library to our device that will allow us to run the model directly on our Arduino Nano 33 BLE Sense.

![Build](assets/images/17-deployment.jpg "Build")

Head to the deployment tab and select **Arduino Library** then scroll to the bottom and click **Build**.

![Build optimizations](assets/images/17-deployment-optimizations.jpg "Build optimizations")

Note that the EON Compiler is selected by default which will reduce the amount of memory required for our model.

![Arduino library](assets/images/17-deployment-download.jpg "Arduino library")

Once the library is built, you will be able to download it to a location of your choice.

## Arduino IDE

![Arduino library](assets/images/18-Arduino-IDE.jpg "Arduino library")

Once you have downloaded the library, open up Arduino IDE, click **Sketch** -> **Include library** -> **Upload .ZIP library...**, navigate to the location of your library, upload it and then restart the IDE.

### Arduino BLE33 SENSE Fusion

Open the IDE again and go to **File** -> **Examples**, scroll to the bottom of the list, go to **Hospital_Bed_Occupancy_Detection_inferencing** -> **nano_ble33_sense** -> **nano_ble33_sense_fusion**.

Once the script opens you will see:

```
#include <Arduino_LSM9DS1.h> //Click here to get the library: http://librarymanager/All#Arduino_LSM9DS1
#include <Arduino_LPS22HB.h> //Click here to get the library: http://librarymanager/All#Arduino_LPS22HB
#include <Arduino_HTS221.h> //Click here to get the library: http://librarymanager/All#Arduino_HTS221
#include <Arduino_APDS9960.h> //Click here to get the library: http://librarymanager/All#Arduino_APDS9960
```

You need to click on each of the library links and install them before you can compile the program and upload. Once you have done this upload the script, open up serial monitor and you will see the output from the program.

![Classifications](assets/images/18-Arduino-IDE-classifications.jpg "Classifications")

Now you can test your program by staying idle, sitting down and standing up. Check the output to see how your program is doing. It should correctly identify each action.

## Versioning

![Versioning](assets/images/19-Versioning.jpg "Versioning")

We can use the versioning feature to save a copy of the existing network. To do so head over to the **Versioning** tab and click on the **Create first version** button.

![Versions](assets/images/19-Versions.jpg "Versions")

This will create a snapshot of your existing model that we can come back to at any time.

# Conclusion

Here we have shown how the Edge Impulse platform combined with the power of the Arduino Nano 33 BLE Sense can be used to create a simple solution that could help hospitals become more efficient.
