---
sidebar_position: 5
---

# Control Camera in BB

In this tutorial, we learn how to set Gain and Exposure time manually on ion-kit based on the previous tutorial that [display 1-camera image](display-image), and 
[display multiple cameras images](display-image-2came)

To control Gain and ExposureTime without BB, you may want to see [Access and Set Device Info](./set-device-info) or [arv-tool-0.8](../../external/aravis/arv-tools).

## Prerequisite

* ionpy 
* numpy
* OpenCV

```bash
pip3 install -U pip
pip3 install opencv-python
pip3 install numpy
pip3 install ion-python
```

## Tutorial

### Get Device Information

To display images with ionpy, we need to get the following information of the device.

* Width
* Height
* PixelFormat

The [previous tutorial](obtain-device-info.md) or [arv-tool-0.8](../../external/aravis/arv-tools.md) will help to get these values.

### Build a pipeline

While the structurte of BB is the same as the [tutorial that display 1-camera image](display-image), and 
[tutorial that display multiple cameras image](display-image-2came), we need some small changes to enable `Gain` and `ExposureTime` setting.

In this tutorial, we can set `Gain` and `ExposureTime` manually by setting `enable_control` to `true`.

While port input is dynamic; i.e. it can be updated for each run, you can set static values in string via Param. For example, the value of `Gain` and `ExposureTime` is set by port to update in each run, and the key of these values are static, so whould be set by Param as follows:

```python
# set params
enable_control = Param('enable_control', 'true')
gain_key = Param('gain_key', 'Gain')
exposure_key = Param('exposure_key', 'ExposureTime')

# set ports
gain_ps = []
exposure_ps = []
for i in range(num_device):
    gain_ps.append(Port('gain' + str(i), Type(TypeCode.Float, 64, 1), 0))
    exposure_ps.append(Port('exposure' + str(i), Type(TypeCode.Float, 64, 1), 0))

gain_values = []
exposure_values = []

for i in range(num_device):
    gain_values.append(40.0)
    exposure_values.append(100.0)

# add enable_control
node = builder.add(bb_name)\
        .set_iport([gain_ps[0], exposure_ps[0]])\
        .set_param([num_devices, frame_sync, realtime_diaplay_mode, enable_control, gain_key, exposure_key]) if num_device == 1 \
        else builder.add(bb_name)\
            .set_iport([gain_ps[0], exposure_ps[0], gain_ps[1], exposure_ps[1]])\
            .set_param([num_devices, frame_sync, realtime_diaplay_mode, enable_control, gain_key, exposure_key])
```
Then, bind the input values to ports. This is similar to bind the output ports and output buffers.
```python
# bind gain values and exposuretime vlaues
for i in range(num_device):
    gain_ps[i].bind(gain_values[i])
    exposure_ps[i].bind(exposure_values[i])
```
Now, we have set `Gain` and `ExposureTime` successfully!

:::tip API updates from v23.11.01
* `set_port` is renamed to `set_iport`
:::

### Execute the pipeline

Execute `builder.run()` to finish the pipeline.

### Update the input port values in each run

In [the previous tutorials](display-image), we have just displayed the output image in each run. This time, you can update the value of `Gain` or `ExposureTime`. The following example shows how to increase `Gain` by `1.0` for every run bi updating gain_values and re-bind the updates.

```python
while(user_input == -1):
    gain_values[0] += 1.0
    gain_ps[0].bind(gain_values[0])

    # running the builder
    builder.run()
    for i in range(num_device):
        output_datas[i] *= coef
        cv2.imshow("img" + str(i), output_datas[i])
    user_input = cv2.waitKeyEx(1)
```
Do not forget to destroy windows that displayed the image after `for` loop.

```python
cv2.destroyAllWindows()
```
## Complete code

Complete code used in the tutorial is [here](https://github.com/Sensing-Dev/tutorials/blob/main/python/python/tutorial2_control_camera.py)