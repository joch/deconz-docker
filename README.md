# deCONZ in Docker

This will run deCONZ in Docker. Please follow the following command (or equivalent) to launch the deCONZ container:

```
`docker run -d --name="deconz" --net="host" -e TZ="Europe/Berlin" -p 8080:8080/tcp -v "/[appdata folder]/deconz":"/root/.local/share/dresden-elektronik/deCONZ":rw --device /dev/ttyUSB0:/dev/ttyUSB0 joch/deconz
```

UPNP discovery is disabled in this container, so to use deconz and Phoscon, use the following URLS:

- deconz: http://[ip]:8080/
- phoscon: http://[ip]:8080/pwa/

Please open an issue if you find any issues running this.

