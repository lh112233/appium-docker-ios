[![Docker Pulls](https://img.shields.io/docker/pulls/quamotion/appium-docker-ios.svg?style=flat-square)](https://hub.docker.com/r/quamotion/appium-docker-ios/)
[![Docker Image](https://images.microbadger.com/badges/image/quamotion/appium-docker-ios.svg)](https://microbadger.com/images/quamotion/appium-docker-ios)
[![Build Status](https://dev.azure.com/qmfrederik/appium-docker-ios/_apis/build/status/quamotion.appium-docker-ios?branchName=master)](https://dev.azure.com/qmfrederik/appium-docker-ios/_build/latest?definitionId=17&branchName=master)

# Appium Docker for iOS

This repository contains the scripts used to build the quamotion/appium-docker-ios
Docker image. It contains all the software required to build and run Appium with iOS
devices on Linux.

The Docker image includes a couple of tools from [Quamotion](http://quamotion.mobi)
which make it possible to run Appium Docker for iOS on Linux. These tools are available
under a commercial license from [Quamotion](http://quamotion.mobi).

## What the container does

If you run a container based on this image, it will:
- Install and launch WebDriverAgent on one iOS device
- Set up an Appium server which can use this instance of WebDriverAgent to automate that iOS device

You can run multiple containers if you want to automate multiple iOS devices in parallel, but each
container instance can only automate a single iOS device.

## Starting the container

Before you can start the appium-docker-ios container, you'll need:

- A running copy of usbmuxd on your host
- A Quamotion license file (request one at http://quamotion.mobi/mwc/license)
- A Developer Profile for signing iOS applications
- The iOS Developer Disk images

Then, start the container using the following command:

```
docker run \
    -p 4723:4723 \
    -v /var/run/usbmuxd:/var/run/usbmuxd \
    -v ${DEVELOPER_PROFILE_PATH}:/quamotion/quamotion.developerprofile \
    -v ${LICENSE_PATH}:/quamotion/.license \
    -v ${DEVELOPER_DISK_PATH}:/quamotion/devimg \
    -e DEVELOPER_PROFILE_PASSWORD=${DEVELOPER_PROFILE_PASSWORD} \
    quamotion/appium-docker-ios
```

Where:

- `DEVELOPER_PROFILE_PATH` is the path to your iOS developer profile.
- `DEVELOPER_PROFILE_PASSWORD` is the password for your iOS developer profile.
- `LICENSE_PATH` is the path to your Quamotion license file.
- `DEVELOPER_DISK_PATH` is the path to the directory which contains the iOS Developer Disk images.

## Starting an Appium session

To start an Appium session on your iOS device:

- Connect to the Appium server running at http://localhost:4723
- Set the following capabilities:
  * `platformName` : `iOS`
  * `automationName` : `XCUITest`
  * `deviceName` : `iPhone`,
  * `udid` : The UDID of your device
  * `bundleId` : `com.apple.Preferences` (to launch the Preferences app)

For example, you could `POST http://localhost:4723/wd/hub/session` with header `Content-Type: application/json`:

```
{
  "desiredCapabilities":
  {
    "platformName":"iOS",
    "automationName":"XCUITest",
    "deviceName":"iPhone",
    "udid":"72157b76f677f22c98864d62307fdff9d56fa62a",
    "bundleId":"com.apple.Preferences"
  }
}
```