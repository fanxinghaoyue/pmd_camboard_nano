Overview
========

This package provides a [ROS][] driver for [PMD[vision]® CamBoard nano][PMD]
depth sensor.

The driver is packaged as a nodelet, therefore it may be directly merged inside
another ROS node to avoid unnecessary data copying. On the same time, it may be
started standalone or withing a nodelet manager.

The `pmd_camboard_nano.launch` script (inspired by the [openni_launch][] stack
in ROS) starts the driver nodelet along with the image rectification and point
cloud generation nodelets.

Installation
============

PMD SDK installation
--------------------

This package requires PMD SDK to be installed in the system. It will search
for:

* header files in `/usr/local/pmd/include`
* shared library in `/usr/local/pmd/lib`
* plugins in `/usr/local/pmd/plugins`

Alternatively, you could put the `include`, `lib`, and `plugins` folders
elsewhere in your file system and set an environment variable `${PMDDIR}` to
point to their location.

You also need to copy the file `10-pmd.rules` provided with the SDK to
`/etc/udev/rules.d` to allow normal users to open the camera.

Package installation
--------------------

Clone this repository into a folder that is in your `$ROS_PACKAGE_PATH`.

ROS API
=======

pmd_camboard_nano::DriverNodelet
--------------------------------

### Published topics

* `depth/camera_info` (*sensor_msgs/CameraInfo*)  
  camera calibration and metadata (see Camera calibration section)

* `depth/image` (*sensor_msgs/Image*)  
  raw distances from the device, contains `float` depths in mm

* `amplitude/camera_info` (*sensor_msgs/CameraInfo*)  
  camera calibration and metadata (see Camera calibration section)

* `amplitude/image` (*sensor_msgs/Image*)  
  signal strengths of active illumination

### Parameters

* `~device_serial` (default: "")  
  specifies which device to open, empty means any

* `~depth_frame_id` (default: "/camera_depth_frame_id")  
  the tf frame of depth camera

* `~open_camera_retry_period` (default: 3)  
  how often to try to open camera during the startup

* `~update_rate` (default: 30)  
  how often to download and publish new data from the camera

### Dynamically reconfigurable parameters

Use the [dynamic_reconfigure][] package to update this parameters in runtime.

* `~remove_invalid_pixels` (default: true)  
  replace invalid pixels in depth and amplitude images with NaNs

* `~integration_time` (default: 333)  
  integration time of the camera in us

* `~averaging_frames` (default: 0)  
  number of frames in sliding averaging window for distance data

* `~bilateral_filter` (default: true)  
  enable/disable bilateral filtering of the depth images

Misc
====

Camera calibration
------------------

The PMD plugin loads the calibration data from a file (provided with the
camera), which must be located within the directory from where the application
is started. If you are using the `pmd_camboard_nano.launch` file, the working
directory of the driver nodelet will be `~/.ros`. You therefore have to have a
copy of the calibration file there.

If the calibration data was not loaded by the PMD plugin, then the camera info
messages produced by the driver nodelet will only have width and height
parameters set, and the rest will be zeroed.

Compatibility
-------------

This package was tested under Ubuntu Precise x64 with ROS Fuerte and under
Ubuntu Oneiric x64 with ROS Electric.

Known issues
------------

If `pmd_camboard_nano.launch` is started with *points* argument set to `true`
(which means calculate and publish point clouds), RViz may crash when trying to
display the messages in the `/points` topic.

Workaround: set display style **NOT** to Points, e.g. to BillboardSpheres.

[ROS]: http://www.ros.org
[PMD]: http://www.pmdtec.com/products-services/pmdvisionr-cameras/pmdvisionr-camboard-nano/
[openni_launch]: http://ros.org/wiki/openni_launch
[dynamic_reconfigure]: http://ros.org/wiki/dynamic_reconfigure
