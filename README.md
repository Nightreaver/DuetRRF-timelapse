# Timelapse videos with your DuetWifi / Duet Ethernet / Duet 2 Maestro 3D printer!

## Requirements

  * DuetWifi or Duet Ethernet or Duet 2 Maestro controlled printer
    - RepRapFirmware v1.21 or v2.0 or higher
    - with enabled WiFi or Ethernet protocol
    - with enabled Telnet protocol
  * Raspberry Pi / Single-Board Computer on the same network as your Duet
    - with Python 3 and the `requests` package
  * Webcam that returns snapshot pictures (still image) via an URL
    - mjpg-streamer or similiar: `http://127.0.0.1:8080/?action=snapshot`
  * Slicer that can insert custom G-code for every new layer
    - for Cura you can use the `TimelapseLayerChange.py` post-processing script. Enable it in the post-processing GUI window, after restarting Cura and copying it into:
      - Linux: `~/.local/share/cura/3.4/scripts`
      - macOS: `~/Library/Application Support/cura/3.4/scripts`
      - Windows: `C:\Users\<username>\AppData\Roaming\cura\3.4\scripts`

## Usage
```
Take snapshot pictures of your DuetWifi/DuetEthernet log_printer on every layer change.
A new subfolder will be created with a timestamp and g-code filename for every new log_print.

This script connects via Telnet to your log_printer, make sure to enable it in your config.g:
    M586 P2 S1 ; enable Telnet

You need to inject the following G-Code before a new layer starts:
    M400 ; wait for all movement to complete
    M118 P4 S"LAYER CHANGE" ; take a picture
    G4 P500 ; wait a bit

If you are using Cura, you can use the TimelapseLayerChange.py script with the Cura Post-Processing plugin.
If you are using Simplify3D, you can enter the above commands in the "Layer Change Script" section of your process.
Slicer-generated z-hops might cause erronously taken pictures, use firmware-retraction with z-hop instead.

After the print is done, use ffmpeg to render a timelapse movie:
    $ ffmpeg -r 20 -y -pattern_type glob -i '*.jpg' -c:v libx264 output.mp4

Usage: ./timelapse.py <folder> <duet_host> <webcam_url> [<auth>] [--no-verify]

    folder       - folder where all pictures will be collected, e.g., ~/timelapse_pictures
    duet_host    - DuetWifi/DuetEthernet hostname or IP address, e.g., mylog_printer.local or 192.168.1.42
    webcam_url   - HTTP or HTTPS URL that returns a JPG picture, e.g., http://127.0.0.1:8080/?action=snapshot
    auth         - HTTP Basic Auth if you configured a reverse proxy with auth credentials, e.g., john:passw0rd
    --no-verify  - Disables HTTPS certificat verification
```
