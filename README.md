# SuperGateway_for_MA35D0

## Intro
This project is IoT reference gateway using Nuvoton's MA35D0(NuMaker-IoT-MA35D0) board and Rafael's EZMesh dongle(Rafael Multi-protocol RCP) based on OpenWrt. EZMesh supports Bluetooth LE 5.3, Thread 1.3, Zigbee 3.0, and Rafael's Sub-G.

## Development environment
|N|Name|Description|Note|
|---|---|---|---|
|1|NuMaker-IoT-MA35D0 V1.1|MA35D0 Evaluation board||
|2|Rafael Micro RT58x EVK|Role of Rafael's EZMesh dongle||
|3|Ubuntu 24.04.02 LTS|Host OS||
|4|Python 3.10.18|Host programming language||

## System Overview
<img width="1028" height="662" alt="image" src="https://github.com/user-attachments/assets/b73e4fab-055d-456f-ac5e-19f4bb3335cc" />
- Rafael's RT582 EVB must be programmed with the RCP firmware and bootloader provided by Rafael Micro.</br>
- For Nuvoton's NuMaker-IoT-MA35D0 board, you need to program the openwrt-ma35d0-iot-256m-sdcard0-pack.bin file into SD0, which you can get by building this project.</br>
- Connect CON7(RMII0) of the MA35D0 board to the Internet.</br>
- Connect CON37(RMII1) of the MA35D0 board to your PC.</br>
- Connect CON9(VCOM) of the MA35D0 board to your PC.</br>
- Connect Rafael's RT582 board and Nuvoton's MA35D0 board via USB. The MA35D0 board can supply power to the RT582 board via USB.</br>
- Connect UART1 (RX, TX, GND) of Rafael RT582 board to UART10 (TX, RX, GND) of Nuvoton MA35D0 board.

## How to build
In the Ubuntu system, open the *Terminal* application and type the following commands:

1. Download SuperGateway source codes:
    ```
    git clone https://github.com/Super-Thomas/SuperGateway_for_MA35D0.git
    cd SuperGateway_for_MA35D0
    ```
2. Setup build system and load the defult config for building :
   ```
   bash scripts/bootstrap
   ```
3. Now, you can build the firmware :
   ```
   bash scripts/build_image -j4 V=s
   ```
4. After the build process completes, you can find openwrt-ma35d0-iot-256m-sdcard0-pack.bin file in openwrt/bin/targets/ma35d0/iot/ directory.

## Network Configuration
You can check the network configuration in the build/file/etc/network file.
```
config interface 'loopback'
        option device 'lo'
        option proto 'static'
        option ipaddr '127.0.0.1'
        option netmask '255.0.0.0'

config globals 'globals'
        option ula_prefix 'fda9:57ec:f637::/48'

config device
        option name 'br-lan'
        option type 'bridge'
        list ports  'eth1'

config interface 'lan'
        option device 'br-lan'
        option proto 'static'
        option ipaddr '10.10.10.1'
        option netmask '255.255.255.0'
        option ip6assign '60'

config interface 'wan'
        option proto 'static'
        option device 'eth0'
        option ipaddr '192.68.20.113'
        option netmask '255.255.255.0'
        option gateway '192.68.20.1'
        list dns '8.8.8.8'
        list dns '8.8.4.4'
```
Please modify the configure options of interface 'wan' for your Internet environment.</br>
Unfortunately, the MA35D0 board doesn't have hardware Wi-Fi. so you need to connect it to PC with wired connection via eth1 port.
Interface 'lan' can connect and communicate with 10.10.10.1 via port eth1.

## Modify ez_config.ini
You should be modify the UART port of ezMesh.</br>
Connect VCOM of MA35D0 board to UART on PC.</br>
Baudrate: 115200 bps / Data: 8-bit / Parity: None / Stop bits: 1 bit / Flow control: None</br>
Then you can press Enter to enter the command.</br>
<img width="1485" height="639" alt="image" src="https://github.com/user-attachments/assets/4f8dcbb9-e42e-4334-8d7a-b9fc8612fc36" /></br>
Open the /usr/etc/ez_config.ini file with the vi command.
```
vi /usr/etc/ez_config.ini
```
Change the port in /usr/etc/ez_config.ini to /dev/ttyS10.</br>
<img width="1485" height="639" alt="image" src="https://github.com/user-attachments/assets/348dbbc2-78df-4681-8714-e014dccdab1b" />
After saving and rebooting like this, you can communicate with EzMesh dongle through UART10 of MA35D0.

## Test
### BLE
1. Enter commands below via UART to the MA35D0 after booting.
```
bluetoothctl
```
2. Search for peripheral devices by entering the 'scan le' command.
```
scan le
```
<img width="989" height="422" alt="image" src="https://github.com/user-attachments/assets/4928e3a7-9b55-4f98-959f-ef55fa759cd3" />
3. Please try using other 'bluetoothctl' options to establish BLE connection and GATT communication.

### Zigbee

## Thanks to
[RafaelMicro SuperGateway](https://github.com/RafaelMicro/SuperGateway)</br>
[Nuvoton OpenWrt](https://github.com/OpenNuvoton/Nuvoton-OpenWrt-22.03)</br>
