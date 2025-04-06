# ESP32 C6 Webserver portal with redirect

No Arduino! IDF only.

This is for ESP32 C6 with 8MB SPI FLASH!
- Change `littlefs` in `partitions.csv`!

Simple captive portal, but serving your custom page by default.
No need to open browser to type IP in manually.

Required:

Use `component registry` to install extras.

1. Setup `littlefs`
   1. Install required: [LittleFS](https://github.com/ARMmbed/littlefs)
      1. `idf.py add-dependency "joltwallet/littlefs^1.19.1"`
2. Create `index.html`
3. Setup Wifi in AP mode
   1. Install required modules from IDF
   2. Add DNS service and redirects

## Serve files

Works in general

1. Create simple partition table first: `idf.py menuconfig` and select 

- `Single factory APP no OTA` OR `Single factory app (large) no OTA`

1. Generate CSV refference next: 

- Gen cmd: `idf.py partition-table` copy generated below
- - Doc: https://docs.espressif.com/projects/esp-idf/en/stable/esp32c6/api-guides/partition-tables.html#generating-binary-partition-table


```text
#### Usual

Partition table binary generated. Contents:
*******************************************************************************
# ESP-IDF Partition Table
# Name, Type, SubType, Offset, Size, Flags
nvs,data,nvs,0x9000,24K,
phy_init,data,phy,0xf000,4K,
factory,app,factory,0x10000,1M,
*******************************************************************************

#### LARGE
*******************************************************************************
# ESP-IDF Partition Table
# Name, Type, SubType, Offset, Size, Flags
nvs,data,nvs,0x9000,24K,
phy_init,data,phy,0xf000,4K,
factory,app,factory,0x10000,1500K,
*******************************************************************************
```

3. New partition for Little FS:

- Copy content into new file at root dir `partitions.csv`
- Add new partition for `littlefs`:
- Optionally change size to simple `1M`, `2M` up to `4M` (I have 8M ESP32 C6)
- Additionally check: **KCONFIG Name:** `ESPTOOLPY_FLASHSIZE` to show youe board actual flash

```csv
# Common partitions above.
# This string was copied from littleFS example: partitions_demo_esp_littlefs.csv
littlefs,  data, littlefs,      ,  0xF0000, 
```

Optional:
- I tried to increase the fs size, since I have 8Mb to spare

```csv
littlefs,data,littlefs,,4M,
```
Compiling...
Working fine:

[Log](logs/partition_and_wifi.log)


4. Try to build with new partition tables.

- Use `idf.py menuconfig` and set `Partition Table` use `Custom partition table CSV` to `partitions.csv`

##### Doc

Partitions and sizes: 
- https://gitdemo.readthedocs.io/en/latest/partition-tables.html#offset-size
- https://docs.espressif.com/projects/esp-at/en/latest/esp32c6/Compile_and_Develop/How_to_customize_partitions.html

FS:
- https://components.espressif.com/components/joltwallet/littlefs/versions/1.19.1
- https://github.com/joltwallet/esp_littlefs
- https://docs.espressif.com/projects/esp-idf/en/latest/esp32/api-reference/storage/spiffs.html



# Create index.html

Use just simple web page.

Now following: https://randomnerdtutorials.com/esp32-esp8266-plot-chart-web-server/


# Else

Set `sdkconfig` or `menuconfig`: `CONFIG_HTTPD_MAX_REQ_HDR_LEN=1024`
Set `sdkconfig` or `menuconfig`: `LWIP_MAX_SOCKETS` to GT 13, or 20.
Set `sdkconfig` or `menuconfig`: `CONFIG_ESPTOOLPY_FLASHSIZE_8MB=y` and `CONFIG_ESPTOOLPY_FLASHSIZE="8MB"`

# TODO
- Add move complex views for webserver, make GET and Ajax for dynamic page update.

# END

## Used examples and refferences:

General examples for simple webserver and portal redirecation:

- https://github.com/espressif/esp-idf/blob/release/v5.4/examples/protocols/http_server/captive_portal/main/main.c
- https://github.com/espressif/esp-idf/blob/release/v5.4/examples/protocols/http_server/simple/main/main.c

To serve `index.html` from SPI memory:

- https://esp32tutorials.com/esp32-esp-idf-spiffs-web-server/
- https://github.com/ESP32Tutorials/ESP32-ESP-IDF-SPIFFS-Web-Server

Use this example to make a more complex setup with requests later.