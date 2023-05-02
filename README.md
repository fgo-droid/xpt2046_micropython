# xpt2046_micropython
Micropython drive for XPT2046 touch, especially for Lilygo T-HMI device

For T-HMI, use the firmware from [russhughes](https://github.com/russhughes/s3lcd/tree/c666663ea9ce005ca8271c470c389054274f0192/firmware/S3LCD_OCT_16M)

This driver is based on the original version from [rdagger](https://github.com/rdagger/micropython-ili9341/blob/d080d5bac95c0da972b26e3599f56bb0311d9ebd/xpt2046.py). The calibration is quite simple (and can be improved in the future).

# Methods
First, a SPI bus must be created.
```
spi = SPI(1, baudrate=1000000)
spi.init(sck=Pin(01), mosi=Pin(03), miso=Pin(04))
cs = Pin(2, mode=Pin.OUT, value=1) 
```

## init
Declare the touch device
```
xpt = Touch(spi, cs=cs, int_pin=int_pin)
```
The `int_pin` argument is mandatory. For T-HMI, use:
```
int_pin = Pin(9)
```

## raw_touch
Provides the raw values from the device, without any reference to the display size. The origin is in the lower right corner. This is only used in the calibration script, but you may not want to use it.
```
x, y = xpt.raw_touch()
```

## get_touch
Provides the x and y coordinates using the display reference. The origin is in the upper left corner.
```
x, y = xpt.get_touch()
```
This method can only be used after calling the `calibrate()` method. Depending on the calibration accuracy, it can provide negative coordinates or coordinates higher than the size of the display. An optional argument is used to clip the provided values to the display size:
```
x, y = xpt.get_touch(True)
```

## calibrate
Arguments are: xmin, xmax, ymin, ymax, width, height, orientation. Use the `Calibration.py` script to get the values. On my T-HMI device, I found that
```
    xmin = 150
    xmax = 1830
    ymin = 150
    ymax = 1830
```
are fairly good.

## is_touched()
Returns `True` if the display was touched or `False` otherwise.

## set_orientation()
Used to change the orientation of the display. Argument values are the same as russhugjes's:

Index | Rotation
----- | --------
0     | Portrait (0 degrees)
1     | Landscape (90 degrees)
2     | Reverse Portrait (180 degrees)
3     | Reverse Landscape (270 degrees)