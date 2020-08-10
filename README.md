# ESP8266 Micropython OTA

Complete guide how to build ota firmware to be used with yaota8266 script.

## Getting Started

Clone this repository:

```
git clone https://github.com/nenadfilipovic/esp8266-micropython-ota
```

I assume you have built your esp-open-sdk and you know how to compile MicroPython firmware.

### Prerequisites

ESP8266 / ESP32 board and Linux operating system.

### Installing

Download yaota8266 repository and extract to esp8266ota folder.

```
https://github.com/pfalcon/yaota8266
```

Download axtls repository and extract to esp8266ota/yaota8266/ota_server/lib/axtls.

```
https://github.com/pfalcon/axtls/tree/3c6b62b718427c03c345ee15be9bb66959a34ccd
```

Generate your keys in ota_client folder:

```
.gen_keys.sh
```

```
python -c "import rsa_sign; rsa_sign.dump_c(rsa_sign.load_key())"
```

Terminal will show you some values and you need `mod` value.
You need to keep priv.key and pub.key in same location as your ota_client.py or else it wont be able to authenticate and ota will fail.

Copy mod value to modulus field in config.h.example and rename file to config.h.

Prepare new sdk and check for updates, change folder to esp-open-sdk and run:

```
make clean
```

```
git pull
```

```
git submodule sync
```

```
git submodule update
```

Copy `etshal.h and boot8266.ld` from patch folder to your boot8266 folder and replace old file.

Copy `ota.ld` from patch folder to your ota_server folder and replace old file.

From root of yaota8266 folder run:

```
make
```

In root folder yaota8266.bin will be created, save it somewhere.

Go to micropython/port/esp8266 folder and run:

```
make ota
```

In build folder firmware-ota.bin will be created, put it next to yaota8266.bin.

Using esptool flash ota server and ota firmware with these commands:

```
esptool.py --port your-port --baud 230400 write_flash -fm dio --flash_size=detect 0x0 yaota8266.bin
```

```
esptool.py --port your-port --baud 230400 write_flash -fm dio --flash_size=detect 0x3c000 firmware-ota.bin
```

To flash firmware by ota, restart esp8266 board and in next 3 seconds press flash button to enter ota mode.
Connect board to internet and get its ip address.

On PC run:

```
ota_client.py -a [address] live-ota [firmware.name]
```

Example:

```
python ota_client.py -a 196.182.12.10 live-ota myfirmware.bin
```

In terminal you will see send and receive requests and hopefully you will see `done` message at the end.


## Running the tests

I have included yaota8266.bin and firmware-ota.bin and keys that work with those images. You can try to test.

### Break down into end to end tests

-

### And coding style tests

-

## Deployment

-

## Built With

* [MicroPython](https://www.micropython.org/download)

## Authors

* **Nenad Filipovic** - *Initial work* - [nenadfilipovic](https://github.com/nenadfilipovic)

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

-
