# Coupled Alarms

[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg)](https://github.com/custom-components/hacs)

This component keeps all defined `alarm_control_panel`s in sync with their states:

* If you disarm one alarm in the group – others will disarm as well.
* If you trigger one alarm in the group – others will trigger as well.
* Etcetera...

## Installation

1. Clone this repo as `coupled_alarms` dir into $HA_CONFIG_DIR/custom_components/:
   ```
   $ cd custom_components
   $ git clone git@github.com:cadavre/coupled_alarms.git ./coupled_alarms
   ```
2. Configure:
   ```
   coupled_alarms:
     my_alarms:
       entities:
         - alarm_control_panel.alarm_one
         - alarm_control_panel.alarm_two
     other_alarms:
       entities:
         - alarm_control_panel.alarm_x
         - alarm_control_panel.alarm_y
         - alarm_control_panel.alarm_z
   ```

## Usage with `miio_gateway`

You can finetune gateway's alarm with `coupled_alarms`.

#### configuration.yml

```yaml
# Standard miio_gateway
miio_gateway:
  host: 192.168.1.2
  port: 54321

# Finetuned custom alarm
alarm_control_panel:
  - platform: manual
    name: Alarm
    pending_time: 0
    delay_time: 0
    disarmed:
      trigger_time: 0
    armed_night:
      trigger_time: 30
    armed_home:
      trigger_time: 30
    armed_away:
      delay_time: 10
      trigger_time: 30

# Alarm coupling
coupled_alarms:
  alarms:
    entities:
      - alarm_control_panel.alarm
      - alarm_control_panel.miio_gateway
```

#### automations.yml

```yaml
- alias: 'Alarm: triggers while at home'
  trigger:
    - platform: state
      entity_id: binary_sensor.motion_garage
      to: 'on'
    - platform: state
      entity_id: binary_sensor.garage_gate_contact
      to: 'on'
    - platform: state
      entity_id: binary_sensor.garage_door_contact
      to: 'on'
  condition:
    - condition: state
      entity_id: alarm_control_panel.alarm
      state: 'armed_home'
  action:
    - service: alarm_control_panel.alarm_trigger
      entity_id: alarm_control_panel.alarm
```

You simply use `alarm_control_panel.alarm` (finetuned) everywhere.
`alarm_control_panel.miio_gateway` will be synchronized automatically.


## Acknowledgement

This repository is forked from the [repository](https://github.com/cadavre/coupled_alarms) written by @cadavre. It only adds the support of being installed as a HACS integration.