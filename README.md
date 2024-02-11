# arlec grid gld130ha RGBWW smartlight esphome
> [!WARNING]
> The cool white and warm white channels aren't working.
> 
> Are they using PWM instead of the sm2135?
>
> If you get it working or have any tips please let me know!

1. Install the esphome kickstart firmware using [tuya cloudcutter](https://github.com/tuya-cloudcutter/tuya-cloudcutter).
   - Select 'By firmware version and name'
   - In my case it was `2.9.16 - BK7231T / oem_bk7231s_light_ty`

2. Fire up esphome with docker:
   ```yaml
   version: "3"
   services:
     esphome:
       container_name: esphome
       image: ghcr.io/esphome/esphome:latest
       volumes:
         - ./configs:/config:rw
         - /etc/localtime:/etc/localtime:ro
       restart: always
       privileged: false
       network_mode: host
   ```

4. Go to the esphome portal (e.g http://localhost:6052), and add a device. Choose any device type

5. Edit the config and replace the esp board config with:
   ```yaml
   bk72xx:
     board: generic-bk7231t-qfn32-tuya
   ```

5. Add this to control the light:
   ```yaml
   sm2135:
     data_pin: P26
     clock_pin: P24
   
   output:
     - platform: sm2135
       id: output_blue
       channel: 0
       max_power: 0.8
     - platform: sm2135
       id: output_green
       channel: 1
       max_power: 0.8
     - platform: sm2135
       id: output_red
       channel: 2
       max_power: 0.8
     - platform: sm2135
       id: output_white
       channel: 3
       max_power: 0.8
     - platform: sm2135
       id: output_warmwhite
       channel: 4
       max_power: 0.8
   
   light:
     - platform: rgbww
       name: "light1"
       red: output_red
       green: output_green
       blue: output_blue
       cold_white: output_white
       warm_white: output_warmwhite
       cold_white_color_temperature: 6536 K
       warm_white_color_temperature: 2000 K
   ```

   Ref: https://esphome.io/components/output/sm2135

6. Save the config and click Install. Choose manual install the first time and download the UF2 package

7. Flash this to the device via OTA. After reboot it should appear in Home Assistant
