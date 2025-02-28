Golioth Light DB LED sample
###########################

Overview
********

This Light DB application demonstrates how to connect with Golioth and access
Light DB.

Requirements
************

- Golioth credentials
- Network connectivity

Building and Running
********************

Configure the following Kconfig options based on your Golioth credentials:

- GOLIOTH_SYSTEM_CLIENT_PSK_ID  - PSK ID of registered device
- GOLIOTH_SYSTEM_CLIENT_PSK     - PSK of registered device

by adding these lines to configuration file (e.g. ``prj.conf``):

.. code-block:: cfg

   CONFIG_GOLIOTH_SYSTEM_CLIENT_PSK_ID="my-psk-id"
   CONFIG_GOLIOTH_SYSTEM_CLIENT_PSK="my-psk"

Platform specific configuration
===============================

QEMU
----

This application has been built and tested with QEMU x86 (qemu_x86) and QEMU ARM
Cortex-M3 (qemu_cortex_m3).

On your Linux host computer, open a terminal window, locate the source code
of this sample application (i.e., ``samples/lightdb_led``) and type:

.. code-block:: console

   $ west build -b qemu_x86 samples/lightdb_led
   $ west build -t run

See `Networking with QEMU`_ on how to setup networking on host and configure
NAT/masquerading to access Internet.

ESP32
-----

Configure the following Kconfig options based on your WiFi AP credentials:

- GOLIOTH_SAMPLE_WIFI_SSID  - WiFi SSID
- GOLIOTH_SAMPLE_WIFI_PSK   - WiFi PSK

by adding these lines to configuration file (e.g. ``prj.conf`` or
``board/esp32.conf``):

.. code-block:: cfg

   CONFIG_GOLIOTH_SAMPLE_WIFI_SSID="my-wifi"
   CONFIG_GOLIOTH_SAMPLE_WIFI_PSK="my-psk"

On your host computer open a terminal window, locate the source code of this
sample application (i.e., ``samples/lightdb_led``) and type:

.. code-block:: console

   $ west build -b esp32 samples/lightdb_led
   $ west flash

See `ESP32`_ for details on how to use ESP32 board.

nRF52840 DK + ESP32-WROOM-32
----------------------------

This subsection documents using nRF52840 DK running Zephyr with offloaded ESP-AT
WiFi driver and ESP32-WROOM-32 module based board (such as ESP32 DevkitC rev.
4) running WiFi stack. See `AT Binary Lists`_ for links to ESP-AT binaries and
details on how to flash ESP-AT image on ESP chip. Flash ESP chip with following
command:

.. code-block:: console

   esptool.py write_flash --verify 0x0 PATH_TO_ESP_AT/factory/factory_WROOM-32.bin

Connect nRF52840 DK and ESP32-DevKitC V4 (or other ESP32-WROOM-32 based board)
using wires:

+-----------+--------------+
|nRF52840 DK|ESP32-WROOM-32|
|           |              |
+-----------+--------------+
|P1.01 (RX) |IO17 (TX)     |
+-----------+--------------+
|P1.02 (TX) |IO16 (RX)     |
+-----------+--------------+
|P1.03 (CTS)|IO14 (RTS)    |
+-----------+--------------+
|P1.04 (RTS)|IO15 (CTS)    |
+-----------+--------------+
|P1.05      |EN            |
+-----------+--------------+
|GND        |GND           |
+-----------+--------------+

Configure the following Kconfig options based on your WiFi AP credentials:

- GOLIOTH_SAMPLE_WIFI_SSID - WiFi SSID
- GOLIOTH_SAMPLE_WIFI_PSK  - WiFi PSK

by adding these lines to configuration file (e.g. ``prj.conf`` or
``board/nrf52840dk_nrf52840.conf``):

.. code-block:: cfg

   CONFIG_GOLIOTH_SAMPLE_WIFI_SSID="my-wifi"
   CONFIG_GOLIOTH_SAMPLE_WIFI_PSK="my-psk"

On your host computer open a terminal window, locate the source code of this
sample application (i.e., ``samples/lightdb_led``) and type:

.. code-block:: console

   $ west build -b nrf52840dk_nrf52840 samples/lightdb_led
   $ west flash

nRF9160 Feather
---------------

On your host computer open a terminal window, locate the source code of this
sample application (i.e., ``samples/lightdb_led``) and type:

.. code-block:: console

   $ west build -b circuitdojo_feather_nrf9160_ns samples/lightdb_led

Enter bootloader and use ``mcumgr`` (or ``newtmgr``) to flash firmware:

.. code-block:: console

   $ mcumgr --conntype serial --connstring /dev/ttyUSB0,baudrate=1000000 build/zephyr/app_update.bin

See `nRF9160 Feather Programming and Debugging`_ for details.

Sample output
=============

This is the output from the serial console:

.. code-block:: console

   [00:00:00.010,000] <wrn> net_sock_tls: No entropy device on the system, TLS communication may be insecure!
   [00:00:00.010,000] <inf> net_config: Initializing network
   [00:00:00.010,000] <inf> net_config: IPv4 address: 192.0.2.1
   [00:00:00.010,000] <dbg> golioth_lightdb.main: Start Light DB LED sample
   [00:00:00.020,000] <inf> golioth_lightdb: Initializing golioth client
   [00:00:00.020,000] <inf> golioth_lightdb: Golioth client initialized
   [00:00:00.020,000] <inf> golioth_lightdb: Starting connect
   [00:00:00.040,000] <inf> golioth_lightdb: Client connected!
   [00:00:00.040,000] <dbg> golioth_lightdb: Payload
                                             a1 63 6d 73 67 62 4f 4b                          |.cmsgbOK
   [00:00:00.040,000] <wrn> golioth_lightdb: Map key is not boolean
   [00:00:00.040,000] <dbg> golioth_lightdb: Payload
                                             a4 61 31 f4 61 32 f5 61  33 f5 61 30 f5          |.a1.a2.a 3.a0.
   [00:00:00.040,000] <inf> golioth_lightdb: LED 1 -> 0
   [00:00:00.040,000] <inf> golioth_lightdb: LED 2 -> 1
   [00:00:00.040,000] <inf> golioth_lightdb: LED 3 -> 1
   [00:00:00.040,000] <inf> golioth_lightdb: LED 0 -> 1

Monitor counter value
=====================

Device increments counter every 5s and updates ``/counter`` resource in Light DB
with its value. Current value can be fetched using following command:

.. code-block:: console

   goliothctl lightdb get <device-id> /counter

Control LEDs
============

Multiple LEDs can be changed simultaneously using following command:

.. code-block:: console

   goliothctl lightdb set <device-id> /led -b '{"0":true,"1":false,"2":true,"3":true}'

This request should result in following serial console output:

.. code-block:: console

   [00:00:04.050,000] <dbg> golioth_lightdb: Payload
                                             a4 61 33 f5 61 30 f5 61  31 f4 61 32 f5          |.a3.a0.a 1.a2.
   [00:00:04.050,000] <inf> golioth_lightdb: LED 3 -> 1
   [00:00:04.050,000] <inf> golioth_lightdb: LED 0 -> 1
   [00:00:04.050,000] <inf> golioth_lightdb: LED 1 -> 0
   [00:00:04.050,000] <inf> golioth_lightdb: LED 2 -> 1

Additionally board LEDs will be changed, if they are configured in device-tree
as:

- ``/aliases/led0``
- ``/aliases/led1``
- ``/aliases/led2``
- ``/aliases/led3``


.. _Networking with QEMU: https://docs.zephyrproject.org/latest/guides/networking/qemu_setup.html#networking-with-qemu
.. _ESP32: https://docs.zephyrproject.org/latest/boards/xtensa/esp32/doc/index.html
.. _AT Binary Lists: https://docs.espressif.com/projects/esp-at/en/latest/AT_Binary_Lists/index.html
.. _nRF9160 Feather Programming and Debugging: https://docs.jaredwolff.com/nrf9160-programming-and-debugging.html
