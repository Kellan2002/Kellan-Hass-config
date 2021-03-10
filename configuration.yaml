default_config:

http:
  ssl_certificate: /ssl/fullchain.pem
  ssl_key: /ssl/privkey.pem
  ip_ban_enabled: true
  login_attempts_threshold: 5

tts:
  - platform: google_translate

group: !include groups.yaml
automation: !include automations.yaml
script: !include scripts.yaml

history:
  include:
    domains:
      - sensor

frontend:
  themes: !include_dir_merge_named themes
logger:
  default: warning
  logs:
    custom_components.adaptive_lighting: debug

recorder:
  db_url: !secret db_url

alarm_control_panel:
  - platform: manual
    name: Alarm
    code: !secret alarm_code
    arming_time: 30
    trigger_time: 300
    disarmed:
      trigger_time: 0
    armed_home:
      delay_time: 0
    armed_away:
      delay_time: 60

homekit:
  - filter:
      include_domains:
        - alarm_control_panel
    entity_config:
      alarm_control_panel.alarm:
        code: !secret alarm_code
    mode: accessory

tplink:
  discovery: false
  switch:
    - host: 192.168.185.221

binary_sensor:
  - platform: mqtt
    name: "Door Sensor"
    state_topic: "cmnd/KBR-Lamp/POWER2"
    payload_on: "ON"
    payload_off: "OFF"
    device_class: door

sensor:
  - platform: qnap
    host: 192.168.185.222
    ssl: true
    port: 443
    verify_ssl: false
    username: !secret qnap_username
    password: !secret qnap_pw
    monitored_conditions:
      - status
      - cpu_usage
      - memory_percent_used
      - network_tx
      - volume_percentage_used
      - volume_size_used

  - platform: netdata
    host: 192.168.185.10
    port: 19999
    name: Proxmox
    resources:
      #      system.load:
      #        data_group: system.load
      #        element: load15

      #      system.cpu:
      #        data_group: "system.cpu"
      #        element: system

      network_download:
        data_group: system.net
        element: InOctets

      network_upload:
        data_group: system.net
        element: OutOctets
        invert: true

  - platform: netdata
    host: 192.168.185.12
    port: 19999
    name: PBS
    resources:
      network_download:
        data_group: system.net
        element: InOctets

      network_upload:
        data_group: system.net
        element: OutOctets
        invert: true

  - platform: template
    sensors:
      proxmox_network_download:
        friendly_name: "Proxmox Network Download"
        unit_of_measurement: "Mbps"
        value_template: "{{ (states('sensor.proxmox_network_download_2') |int / 8000 )| round(2)}}"

  - platform: template
    sensors:
      proxmox_network_upload:
        friendly_name: "Proxmox Network Upload"
        unit_of_measurement: "Mbps"
        value_template: "{{ (states('sensor.proxmox_network_upload_2') |int / 8000 )| round(2)}}"

  - platform: template
    sensors:
      pbs_network_download:
        friendly_name: "PBS Network Download"
        unit_of_measurement: "Mbps"
        value_template: "{{ (states('sensor.pbs_network_download') |int / 8000 )| round(2)}}"

  - platform: template
    sensors:
      pbs_network_upload:
        friendly_name: "PBS Network Upload"
        unit_of_measurement: "Mbps"
        value_template: "{{ (states('sensor.pbs_network_upload') |int / 8000 )| round(2)}}"

light:
  - platform: mqtt
    name: "Bedroom Lamp"
    command_topic: "cmnd/KBR-Lamp/power"
    state_topic: "stat/KBR-Lamp/POWER"
    qos: 1
    payload_on: "ON"
    payload_off: "OFF"
    retain: true

  - platform: mqtt
    name: "Julia's Bedroom Lamp"
    command_topic: "cmnd/JBR-Lamp/power"
    state_topic: "stat/JBR-Lamp/POWER"
    qos: 1
    payload_on: "ON"
    payload_off: "OFF"

  - platform: mqtt
    name: "Lounge Lamp"
    command_topic: "cmnd/L-Lamp/power"
    state_topic: "stat/L-Lamp/POWER"
    qos: 1
    payload_on: "ON"
    payload_off: "OFF"

switch:
  - platform: mqtt
    name: "Conservatory Fan"
    command_topic: "cmnd/C-Fan/power"
    state_topic: "stat/C-Fan/POWER"
    qos: 1
    payload_on: "ON"
    payload_off: "OFF"
    retain: true

#sensor:
#  - platform: mqtt
#    state_topic: 'tele/KBR-Lamp/SENSOR'
#    name: 'Bedroom Temp'
#    unit_of_measurement: 'C'
#    value_template: '{{ value_json.AM2301.Temperature }}'
#
# - platform: mqtt
#    state_topic: 'tele/KBR-Lamp/SENSOR'
#    name: 'Bedroom Humidity'
#    unit_of_measurement: '%'
#    value_template: '{{ value_json.AM2301.Humidity }}'

yeelight:
  devices:
    192.168.185.143:
      name: Light Strip
      transition: 1000
      use_music_mode: true
      save_on_change: true

  custom_effects:
    - name: "Fire Flicker"
      flow_params:
        count: 0
        transitions:
          - TemperatureTransition: [1900, 1000, 80]
          - TemperatureTransition: [1900, 2000, 60]
          - SleepTransition: [1000]
    - name: "Sunrise"
      flow_params:
        count: 1
        transitions:
          - RGBTransition: [255, 77, 0, 50, 1]
          - TemperatureTransition: [1700, 360000, 10]
          - TemperatureTransition: [2700, 540000, 100]
    - name: "Sunrise"
      flow_params:
        count: 1
        action: stay
        transitions:
          - RGBTransition: [255, 77, 0, 50, 1]
          - TemperatureTransition: [1700, 3000, 10]
          - TemperatureTransition: [6500, 3000, 100]

camera:
  - platform: ffmpeg
    name: Conservatory Camera
    input: !secret conservatory_camera

  - platform: ffmpeg
    name: Entrance Camera
    input: !secret entrance_camera

  - platform: ffmpeg
    name: Carport Camera
    input: !secret carport_camera

  - platform: ffmpeg
    name: Laundry Camera
    input: !secret laundry_camera

  - platform: ffmpeg
    name: Garden Camera
    input: !secret garden_camera

  - platform: ffmpeg
    name: Pool Camera
    input: !secret pool_camera

ios:
  push:
    categories:
      - name: cameras
        identifier: "camera"
        actions:
          - identifier: "TRIGGER_ALARM"
            title: "Trigger Alarm"
            destructive: "true"

media_player:
  - platform: samsungtv_tizen
    host: 192.168.185.200
    name: Lounge TV
    port: 8002
    mac: !secret samsung_tv_mac
    app_list: '{"Netflix": "11101200001", "YouTube": "111299001912", "Plex": "3201512006963", "DSTv": "3201804016109"}'
    show_logos: "white-color"

#device_tracker:
#  - platform: iphonedetect
#   consider_home: 12
#   scan_interval: 12
#    new_device_defaults:
#      track_new_devices: true
#    hosts:
#      kellaniphone: 192.168.185.20
