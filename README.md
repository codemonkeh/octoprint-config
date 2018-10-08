# octoprint config

## GCODE Scripts

### Before print job starts
### After print job completes

    M17; enable motors
    G1 X0 Y300 F3600; move to back left corner
    M18; disable motors

### After print job is cancelled

    ; disable motors
    M84

    ;disable all heaters
    {% snippet 'disable_hotends' %}
    {% snippet 'disable_bed' %}
    ;disable fan
    M106 S0

### After print job is paused

    {% if pause_position.x is not none %}
    ; relative XYZE
    G91
    M83

    ; retract filament, move Z slightly upwards
    G1 Z+5 E-5 F4500

    ; absolute XYZE
    M82
    G90

    ; move to a safe rest position, adjust as necessary
    G1 X0 Y0
    {% endif %}

### Before print job is resumed

    {% if pause_position.x is not none %}
    ; relative extruder
    M83

    ; prime nozzle
    G1 E-5 F4500
    G1 E5 F4500
    G1 E5 F4500

    ; absolute E
    M82

    ; absolute XYZ
    G90

    ; reset E
    G92 E{{ pause_position.e }}

    ; move back to pause position XYZ
    G1 X{{ pause_position.x }} Y{{ pause_position.y }} Z{{ pause_position.z }} F4500

    ; reset to feed rate before pause if available
    {% if pause_position.f is not none %}G1 F{{ pause_position.f }}{% endif %}
    {% endif %}

### Before tool change
### After tool change
### After connection to printer is established

    M92 E95 ;set default extrusion steps to 95, a better default

### Before connection to printer is closed
