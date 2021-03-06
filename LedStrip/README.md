# Dont build this....

Im currently in the process of re-designing this board based on the tasmota
firmware so I would hold off building one of these boards in favour of my new
uTasmota board - very similar size, and more flexible.

If you really do want to build this board, go ahead, but rather than using
my own homie-based firmware, flash the tasmota firmware (make sure you using
a board that has 1mb of flash, not 512k).

.... continueing on...



# Led strip for ESP8266 with homie and hardware

This is a little code base and a hardware board designed to work with homie
and the WS2812 led strips. The hardware component is based on the ESP-01, that
is you print the board, put its components on, then add an ESP-01 (or s).

* [OSHPark Project](https://oshpark.com/shared_projects/sWWzDNyt)
* [Small Demo Video](https://www.youtube.com/watch?v=C1is4zkPhkE)
* [Pointer and Fire Modes](https://www.youtube.com/watch?v=obZJblPsRek)
* [Putting it together video](https://youtu.be/5BQnyncV6P0)

Technically, the board could be used to drive anything if all you need is 5v
input, 1x5v GPIO and 3 x 3.3v GPIO (TX/RX can be used for multiple things, 
including I2C or just straight GPIO). Note though that GPIO2 drives the 5v
output and care must be taken because pulling GPIO2 low on boot, causes it to
boot in a different mode!

## The Board:
![Board](https://raw.githubusercontent.com/takigama/HomeAutomationExperiments/master/LedStrip/images/esp-01-ledstripcontroller-02.jpg)
![Board2](https://raw.githubusercontent.com/takigama/HomeAutomationExperiments/master/LedStrip/images/esp-01-ledstripcontroller-04.jpg)

## Messages
- devices/#DEVICE_ID/switch/switch/set - changes on/off
- devices/#DEVICE_ID/switch/status - reports status here
- devices/#DEVICE_ID/brightness/brightness/set - change brightness (0-255)
- devices/#DEVICE_ID/color/color/set - changes the colour state (R,G,B)
- devices/#DEVICE_ID/color/color - colour report

Can be used with ranges, i.e. sending 0,20,on to switch will only turn on
leds 0 to 20 - isnt required

## Home Assistant config
looks very roughly like this (its a moving target):
```
light:
  - platform: mqtt
    name: "esplight"
    command_topic: "devices/dev1/switch/switch/set"
    rgb_command_topic: "devices/dev1/led/color/set"
    rgb_value_template: "value.rgb|join(',')"
    brightness_state_topic: "devices/dev1/brightness/brightness/set"
    brightness_command_topic: "devices/dev1/brightness/brightness/set"
    qos: 0
    payload_on: "on"
    payload_off: "off"
    optimistic: false
    state_topic: "devices/dev1/switch/status"

```


## work in progres
Note, i just started this in late october - its very much a work in progress!

## TODO's

### DONE
1. Moved to fastled lib (not really liking it much) - DONE
2. change messages to allow for sub-ranges - DONE
3. add some fixed effects (fire, pointer, tv)

### Not Done
4. make number of leds dynamic (and saved in eeprom) - trickier than i'd hoped





## Hardware
The kicad project is just a little daughter board for an ESP-01 that makes
it easier to control ws2812's. 5V input with a reg on board and a level
shifter for the signal line - see the bom file (html format) for the components
to get on the board. Its much easier to make than it looks, dont let SMD
scare you!

### Alternative Parts

The board uses an MCP1700 Reg to create the 3.3v needed by the board. This can
be replaced with a different REG to support higher input voltages (6v with the
MCP1700), but can be something like an [MCP1703T](http://www.farnell.com/datasheets/1268705.pdf?_ga=1.231719964.246887079.1486429056)
which can handle up to 16v (for 12v led strips?)

In the built board, i've used a 100uf 6v CAP (as per recommendations, but anything
that fits is suitable, there are 0.1F 5.5v caps that are quite small and would also
fit on board.

Parts from the bom:
* BSS138 (SOT-23 package)
* 2-10uf 6v+ Tant x 2 (1210 package)
* 0.1-2uf 6v+ Ceramic x 2 (0805 package)
* MCP1700 3.3v LDO Reg (SOT-23 package)
* 10k resistor x 2 (0805 package)
* ESP-01 x 1
* Bunch of pin headers

You can also either provide female pin headers for the ESP-01 or
just solder it direct to the board (more compact permanently soldered)


## Pin Layout

![Pin Layout](https://raw.githubusercontent.com/takigama/HomeAutomationExperiments/master/LedStrip/images/board_v0.3.png);

## Attributions
Level shifter is a reproduction of sparkfun's bi-directional level shifter, as
available here:

[SparkFun Logic Level Converter](https://www.sparkfun.com/products/12009)
