# Calibration

Creator: frix-x <https://github.com/Frix-x>\
Edited and modified: Fragmon <https://github.com/Fragmon>

## Description

This set of macros (and associated bash script) is intended to help configure calibration within Klipper.

## Installation

  1. copy the macro file "calibrate_2.0.cfg" directly into your own configuration.
  2. make sure that the Klipper extension 'gcode_shell_command.py' is installed.
  The easiest way to install it is to use the advanced section of KIAUH.\
  [Install KIAUH](https://www.obico.io/blog/install-klipper-with-kiauh/#install-kiauh-on-your-raspberry-pi)\
  Start KIAUH -> 4) [Advanced] -> 8) [G-Code Shell Command]
  3. paste the `graph_vibrations.py` and `plot_graphs.sh` into the root directory of your own configuration (i.e. into your `~/printer_data/config/scripts` directory).
     Note: If you use Windows to copy/paste the files, pay attention to the line endings for the `plot_graphs.sh` file and the `graph_vibrations.py` file.
  4. make the scripts executable with SSH. If you are in the folder, use:

     ```bash
     chmod +x ./plot_graphs.sh
     chmod +x ./graph_vibrations.py
     ```

  5. (Optional) You can change the first lines of the `plot_graphs.sh` script to configure where you want to store the results.
  Default: `~/printer_data/config/adxl_results`.
  6. The start code must be adapted to your own variables.
  7. (optional) The default values can be adjusted in the respective macro.

## Functionality

  `FLOW_MULTIPLIER_CALIBRATION`: Determines the optimal extrusion factor. This can be stored in the slicer.
    COMPUTE_FLOW_MULTIPLIER: Used to calculate the optimal extrusion factor.

  `PRESSURE_ADVANCE_CALIBRATION`: Determines the optimal PA. This can be stored in the slicer.
  
  `MAX_FLOW_CALIBRATION`: Determines the maximum flow under the set settings.
  
  `AXES_SHAPER_CALIBRATION`: Determines the optimal input shaper values. These can be stored in the printer.cfg.
  
  `VIBRATIONS_CALIBRATION`: Determines the vibrations depending on the speed. Should only be executed with activated input shaper.
  
  `BELTS_SHAPER_CALIBRATION`: Determines the tension of the belts. Only usable for CoreXY systems.
