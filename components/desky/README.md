# Desky sit/stand desk component

This component provides support for reading the Desky sit/stand desk height and moving it to specific heights

## Installation

```yaml
external_components:
  - source:
      type: git
      url: https://github.com/gene891212/custom-esphome-components
      ref: main
    components: [desky]
```

## Usage with the full sit/stand workflow (recommended)

[packages/desky-workflow.yaml](../../packages/desky-workflow.yaml) bundles this component together with the pin
outputs, memory-button presets, and sit/stand scheduling automation used for a real desk. Reference it from your
device YAML and only override the substitutions that differ from your wiring:

```yaml
substitutions:
  desky_up_pin: GPIO8
  desky_down_pin: GPIO7
  desky_purple_pin: GPIO10
  desky_uart_rx_pin: GPIO44

packages:
  desky_workflow:
    url: https://github.com/gene891212/custom-esphome-components
    file: packages/desky-workflow.yaml
    ref: main
    refresh: 1d
```

A full device config example is at [examples/desky-standing-desk.yaml](../../examples/desky-standing-desk.yaml).

## Component config reference

Example:
```yaml
uart:
  - id: desk_uart
    baud_rate: 9600
    rx_pin: 3

desky:
  #uart_id: desk_uart  (optional, unless multiple uarts are defined)
  id: my_desky
  height:  # optional sensor publishing the current height
    name: Desk Height
    # any other sensor options
  up:    # optional <pin> config
    number: 4
    inverted: true  # probably needed
  down:  # optional <pin> config
    number: 5
    inverted: true  # probably needed
  request:  # optional <pin> config to request height updates at boot
    number: 12
    inverted: true  # probably needed
  stopping_distance: 15  # optional distance from target to turn off moving, default 15
  timeout: 15s  # optional time limit for moving, default is none

on_...:
  then:
    - lambda: id(my_desky).move_to(150);
    - lambda: id(my_desky).stop();

binary_sensor:
  - platform: template
    name: Desky moving
    lambda: return id(my_desky).current_operation != desky::DESKY_OPERATION_IDLE;
```
