# Copy-Bulb
Home Assistant script to copy one bulb to another with random colour and brightness variations.
I wrote this because we have a couple of places that I wanted one or two particular light bulbs to be set to a different (but pleasant) contrast to the other bulbs in the room.
We have two outside lights on a porch, and a third light above the house number plate - no matter what colours and settings the two porch lights are at I wanted the plate light to be a contrast to them.
In another use case there is a room with four ceiling lights and a table lamp, I wanted the table lamp to be the same colour as the ceiling lights but always slightly brighter and richer colour.
Rather than implementing this as an automation, I've done it as a script that can then be used multiple times by several autoamtions in different situations.

# Install
Just copy and paste the copy-bulb.yaml script into your Home Assistant script editor in YAML mode and call it 'Copy Bulb' (so it becomes script.copy_bulb).
Then create some automations that use it.

# Examples
Here is an automation that uses the script to copy the colour of one of the porch bulbs to the bulb above the house number plate, using a tetradic colour, making it slightly brighter and less saturated:

```
alias: House Number Light Controller
description: Change the colour of the house number light on to stand out.
triggers:
  - entity_id:
      - light.porch_light_1
    attribute: hs_color
    for:
      hours: 0
      minutes: 0
      seconds: 30
    trigger: state
    id: Colour
  - entity_id:
      - light.porch_light_1
    from: "on"
    to: "off"
    trigger: state
    id: "Off"
  - entity_id:
      - light.porch_light_1
    from: "off"
    to: "on"
    trigger: state
    id: "On"
conditions: []
actions:
  - delay:
      hours: 0
      minutes: 0
      seconds: 5
      milliseconds: 0
  - if:
      - condition: trigger
        id:
          - "Off"
    then:
      - action: homeassistant.turn_off
        metadata: {}
        data: {}
        target:
          entity_id: light.house_number
    else:
      - data:
          from: light.porch_light_1
          to: light.house_number
          hue_mode: tetradic
          brightness_mode: brighter
          sat_mode: desaturate
        action: script.copy_bulb
mode: restart
```
