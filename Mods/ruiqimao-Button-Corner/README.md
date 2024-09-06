# Button Corner

![Animation](Images/Animation.gif)

This mods adds a stealthy integrated button into a skirt corner. In addition to the parts required for a regular skirt corner, this mod requires:

| Part | Quantity |
|:-|:-:|
| M3x10 SHCS | 1 |
| M2x10 self tapper | 1 |
| 3mm ID x 4mm OD x 7mm length PTFE tube | 1 |
| D2F-L microswitch | 1 |

## Klipper configuration

This sample configuration can be used to customize the behavior of single, double, triple, and long presses of the button.

```
[gcode_button action]
pin: # INSERT PIN.
press_gcode: _ACTION_PRESS
release_gcode: _ACTION_RELEASE

[gcode_macro ACTION_BUTTON]
variable_count: 0
variable_delay = 0.2
variable_long = 1.0
gcode:
    {% set long = params.LONG|default(0)|int %}
    {% if long == 1 %}
        # INSERT LONG PRESS GCODE.
    {% else %}
        {% if count == 1 %}
            # INSERT SINGLE PRESS GCODE.
        {% endif %}
        {% if count == 2 %}
            # INSERT DOUBLE PRESS GCODE.
        {% endif %}
        {% if count >= 3 %}
            # INSERT TRIPLE PRESS GCODE.
        {% endif %}
    {% endif %}

[gcode_macro _ACTION_PRESS]
gcode:
    {% set vars = printer["gcode_macro ACTION_BUTTON"] %}
    _ACTION_CANCEL_DELAY
    SET_GCODE_VARIABLE MACRO=ACTION_BUTTON VARIABLE=count VALUE={vars.count|int + 1}
    UPDATE_DELAYED_GCODE ID=_ACTION_LONG_EXECUTE DURATION={vars.long}

[gcode_macro _ACTION_RELEASE]
gcode:
    {% set vars = printer["gcode_macro ACTION_BUTTON"] %}
    _ACTION_CANCEL_DELAY
    UPDATE_DELAYED_GCODE ID=_ACTION_EXECUTE DURATION={vars.delay}

[gcode_macro _ACTION_RESET]
gcode:
    SET_GCODE_VARIABLE MACRO=ACTION_BUTTON VARIABLE=count VALUE=0

[gcode_macro _ACTION_CANCEL_DELAY]
gcode:
    UPDATE_DELAYED_GCODE ID=_ACTION_EXECUTE DURATION=0
    UPDATE_DELAYED_GCODE ID=_ACTION_LONG_EXECUTE DURATION=0

[delayed_gcode _ACTION_EXECUTE]
gcode:
    _ACTION_CANCEL_DELAY
    ACTION_BUTTON
    _ACTION_RESET

[delayed_gcode _ACTION_LONG_EXECUTE]
gcode:
    _ACTION_CANCEL_DELAY
    ACTION_BUTTON LONG=1
    _ACTION_RESET
```
