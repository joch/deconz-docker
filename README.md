# deCONZ in Docker

This will run deCONZ in Docker. Please follow the following command (or equivalent) to launch the deCONZ container:

```
docker run -d --name="deconz" --net="host" -e TZ="Europe/Berlin" -p 8080:8080/tcp -v "/[appdata folder]/deconz":"/root/.local/share/dresden-elektronik/deCONZ":rw --device /dev/ttyUSB0:/dev/ttyUSB0 joch/deconz
```

UPNP discovery is disabled in this container, so to use deconz and Phoscon, use the following URLS:

- deconz: http://[ip]:8080/
- phoscon: http://[ip]:8080/pwa/

You can change the default port from 8080 by providing the environment variable `DECONZ_WEB_PORT` like this:
```
docker run -d --name="deconz" --net="host" -e TZ="Europe/Berlin" -e DECONZ_WEB_PORT=8090 -p 8090:8090/tcp -v "/[appdata folder]/deconz":"/root/.local/share/dresden-elektronik/deCONZ":rw --device /dev/ttyUSB0:/dev/ttyUSB0 joch/deconz

```
The same goes for deconz web sockets port `DECONZ_WS_PORT`

Please open an issue if you find any issues running this.

## Updating the firmware

When upgrading to a new version, you will usually be prompted to update the firmware of the conbee stick as well. To find the firmware file to use, just replace the hex value below with what deconz/phoscon tells you to update to. Follow this procedure:

```
docker stop deconz
rmmod ftdi_sio
docker run --rm -ti --privileged=true --device /dev/ttyUSB0:/dev/ttyUSB0 -v /dev/bus/usb:/dev/bus/usb joch/deconz GCFFlasher_internal -d 0 -f /usr/share/deCONZ/firmware/deCONZ_Rpi_0x261d0500.bin.GCF
```

It will output the following (ignore the not found warnings, it’s fine):

```
/usr/bin/GCFFlasher_internal: 9: /usr/bin/GCFFlasher_internal: rmmod: not found
/usr/bin/GCFFlasher_internal: 10: /usr/bin/GCFFlasher_internal: rmmod: not found
GCFFlasher V2_11 (c) dresden elektronik ingenieurtechnik gmbh 2017/12/10
using firmware file: /usr/share/deCONZ/firmware/deCONZ_Rpi_0x261d0500.bin.GCF
reset via FTDI

flashing 123252 bytes: |=============================|
verify: ....
SUCCESS
/usr/bin/GCFFlasher_internal: 14: /usr/bin/GCFFlasher_internal: modprobe: not found
/usr/bin/GCFFlasher_internal: 15: /usr/bin/GCFFlasher_internal: modprobe: not found
```

Then restart:

```
modprobe ftdi_sio
docker restart deconz
```

Now your Conbee stick should be updated and you should be ready to go.

## Example using docker-compose:

This example shows how to using this image with docker-compose.

```yaml
  deconz:
    container_name: deconz
    hostname: deconz
    image: joch/deconz
    restart: always
    environment:
      - TZ="Europe/Stockholm"
      - DECONZ_WEB_PORT=8080  # This is the default web (http) port
      - DECONZ_WS_PORT=443    # This is the default web sockets port
    ports:
      - "8080:8080"
      - "443:443"
    volumes:
      - /deconz:/root/.local/share/dresden-elektronik/deCONZ:rw
    devices:
      - /dev/ttyUSB0:/dev/ttyUSB0
```
