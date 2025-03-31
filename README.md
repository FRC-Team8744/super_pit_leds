# Super Pit LEDS

## Description

The intention behind this project is to control addressable LEDS on pit structures in a way to project extra light and meaningful visual information to the team.
It uses [ESPHome](https://esphome.io/) to configure ESP32 micro controllers that drive the LEDS, connect and communicate to them via Bluetooth, and pull data from reputable FRC event status applications such as [frc.Nexus](https://frc.nexus/en/api).

## Bill of Matterials

- ESP32 Micro Cntrollers x2 + however many other controllers are need to drive the LEDS the base configuration here is x2 WLED units.
    - Optionally the LED controllers can be WLED boxes purpose built to drive LEDS, make sure there is a way to program them.
- Momentary Switches (Optional) x5
- Toggle Switch (Optional) x1
- Variable Resistor (Optional) x4
    - Capacitor x4
- Addressable LEDS (Enough to cover where you LEDS are wanted)
- Power supply apporiate for your LEDS and micro controller. The base configuration here is x3, x2 12v and x1 5v.

## Getting Started

### Install ESPHome

There are a few methods to install the ESPHome development environment, but the two that I would recommend either manual or docker.
- [Docker](https://esphome.io/guides/getting_started_command_line#installation)
- [Manual](https://esphome.io/guides/installing_esphome)

Start the Device Builder which is a development environment to help with autocomplete and other IDE based quality of life features.
- [Start Device Builder](https://esphome.io/guides/getting_started_command_line#bonus-esphome-device-builder)

Note: When starting the Device Builder, make sure to be in this project.

### Setup the Environment

In the Device Builder there is a button called Secrets in the upper right corner. When opened enter the below enteries replacing the values with ones specific with your setup.
- ap_pass: WIFI password when directly connecting to the Pit Control Web device, specificilly to connect it to WIFI.
- web_user: The user name when connecting to the Pit Control Web device using a browser.
- web_pass: The password when connecting to the Pit Control Web device using a browser.
- default_nexus_api_key: The API Key obtained from [frc.Nexus](https://frc.nexus/en/api), this can later be changed in the Pit Control Web page.
- ota_pass: A password required for over-the-air updates, this is to prevent others from changing the software when they shouldn't be.
Example:
ap_pass: pits-are-awesome
web_user: users-rock
web_pass: whos-going-to-get-this
default_nexus_api_key: 393fa9348e
ota_pass: 409jaergf;ajwewi4jtrasr;ijasdflkjawergasdjfasl

In the Device Builder there is a file called substitutions.yaml. In here you will find substitutions for basic things to change.
- team_number: '8744'
- team_color_rgb: '53, 67, 169'
- default_event_id: '2025wila'
- led_type: 'ws2811'
- led_count: '42'
- led_order: 'RGB'

After configuring the Pit Led modules, look at the logs and find the BLE Mac address, this will be needed in the led_ble_client.yaml so that it can connect.

## Electrical

### Pit Leds

- This module hosts a BLE Server for Pit Control to connect to
- 12v WLED modules pin out
    - GPIO0  - The button on the WLED modules to cylce through the colors and alliance effects
    - GPIO2  - Lower PED Strip Data Pin
    - GPIO16 - Upper LED Strip Data Pin

### Pit Control

#### BLE Side of Pit Control

- Connects to Pit LEDs using a BLE Client
- Variable Resistors need to have a capacitor that sits accross VCC and Ground to help smooth out fluxuations in the power.
- Buttons should be connected to ground
- ESP32-DevKit pin out
    - 3.3V   - Power to the Variable Risistors, capacitors, and WIFI Side of Pit Control
    - Ground - Ground side of the Variable Risistors, capacitors, buttons, and WIFI Side of Pit Control
    - GPIO2  - Blue alliance effect from WIFI side of Pit Control
    - GPIO12 - Button to activate the blue alliance effect 
    - GPIO13 - Button to turn off the LEDS
    - GPIO14 - Unused button for future use
    - GPIO15 - Red alliance effect from WIFI side of Pit Cotnrol
    - GPIO25 - Button to activate the red alliance effect
    - GPIO26 - Button to activate the team color
    - GPIO27 - Button to change color to white
    - GPIO32 - Red color adjust variable resistor swipper pin
    - GPIO33 - Brightness adjust variable resistor swipper pin
    - GPIO34 - Blue color adjust variable resistor swipper pin
    - GPIO35 - Green color adjust variable resistor swipper pin

#### WIFI Side of Pit Control

- Connects to [frc.Nexus](https://frc.nexus/en/api) over WIFI
- Hosts an AP to connect to if WIFI can not be found to connect to
- Hosts a Web server page once on WIFI so that some of the settings can be changed
- ESP32-DevKit pin out
    - 3.3V   - Power from the BLE Side of Pit Control
    - Ground - Ground from the BLE Side of Pit Control
    - GPIO2  - Activates Blue alliance effect
    - GPIO15 - Activates Red alliance effect

## Housing

The model currently being used for the box.
- [Box for Pit Control](https://cad.onshape.com/documents/1340fe07a8e6283332d42695/w/8fcb2ded6eca48f73eb9c5ef/e/9fae97c2bc773bd321d171a2)

## Future change considerations

- Change the communication between Pit Control and Pit Control Web to I2C
    - This will allow for more complex communication
- Flip the BLE Client and Server Roles, e.g. make Pit Control the Server and Pit LED modules the client.
    - This will help with setup so that the controller can control any number of Pit LED modules while only requiring one BLE Mac to be known
    - Maybe this will make it so that only one ESP32 is needed to for Pit Control
- Swap the Variable Resistors for Rotory Encoders.
    - This will allow for reletive adjustments to the lights instead of fix values we get from the Variable Resistors.
- Program 6th button to stop the current effect.
    - Currently they only way to do that is to switch to one of the preset colors, white or the team color
