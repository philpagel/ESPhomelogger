# ESPhomelogger
Log entity states of ESPhome devices without HASSIO

Devices running [ESPhome](https://github.com/esphome/esphome) firmware can
easily be integrated into [Homeassistant](https://www.home-assistant.io/)
but sometimes, I want to use them standalone or access them outside of HASSIO
for other reasons. 

This little script uses the ESPhome native API to get the state of various
entities implemented in the device and write the data to STDOUT as a tab
separated table (TSV) for easy use in downstream analysis.

Unlike the example script from
[https://github.com/esphome/aioesphomeapi](https://github.com/esphome/aioesphomeapi) 
it translates the entity keys to their names and output is trivially parsed by 
other software.

# Dependencies

Install dependencies with `pip3` like this:
    
    pip3 install -r requirements.txt


# Running

Either use

    python3 esphomelogger

or better make the file executable

    chmod a+x esphomelogger 

so you can run it as usual:

    ./esphomelogger

## Available options

Usage and available options can be shown with the `--help` option:

    Usage: esphomelogger [OPTIONS] HOST

    Options:
      -p, --port INTEGER   API Port on ESPhome device
      -a, --apikey TEXT    API key
      -P, --password TEXT  API password. THIS IS DEPRECATED. Please move to API
                           keys.

      -d, --delim TEXT     Field delimiter [\t]
      --help               Show this message and exit.

API passwords have been deprecated recently in Homeassistant/ESPhome.
Please update all you ESPhome devices accordingly!

If you still have a device that supports password authentication, you must give
the password on the command line with the `-P` option *AND* provide a non-empty
string as an API key although the latter will not be used. Please upgrade all
your ESPhome devices to key authentication – the password option will be
removed from this tool, soon.

# Example session

Here is a little example session in which I query data from a smartplug (hostname 
`test-plug`) that has power metering functionality:

    ❯ ./esphomelogger test-plug -a a98SDf7982h318U09u341ph13498=u
    timestamp	variable	value
    2023-01-07T13:14:49	test-plug - Status	True
    2023-01-07T13:14:49	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:14:49	test-plug - Voltage	227.00172424316406
    2023-01-07T13:14:49	test-plug - Ampere	0.0
    2023-01-07T13:14:49	test-plug - Power	0.0
    2023-01-07T13:14:49	test-plug - Wifi Signal	-59.0
    2023-01-07T13:14:49	test-plug - Uptime	60352.62109375
    2023-01-07T13:14:49	test-plug - Switch	True
    2023-01-07T13:14:49	test-plug - IP Address	192.168.0.55
    2023-01-07T13:14:49	test-plug - Wi-Fi SSID	Foolan
    2023-01-07T13:14:49	test-plug - Wi-Fi BSSID	B0:39:56:E3:AA:B0
    2023-01-07T13:14:49	test-plug - ESPHome Version	2022.8.0
    2023-01-07T13:14:52	test-plug - Voltage	227.08299255371094
    2023-01-07T13:14:52	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:14:52	test-plug - Power	0.0
    2023-01-07T13:14:54	test-plug - Voltage	227.04234313964844
    2023-01-07T13:14:54	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:14:54	test-plug - Power	0.0
    2023-01-07T13:14:57	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:14:57	test-plug - Power	0.0
    2023-01-07T13:15:01	test-plug - Ampere	0.0
    2023-01-07T13:15:01	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:15:01	test-plug - Power	0.0
    2023-01-07T13:15:03	test-plug - Ampere	0.0
    2023-01-07T13:15:03	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:15:03	test-plug - Power	0.0
    2023-01-07T13:15:06	test-plug - Ampere	0.0
    2023-01-07T13:15:06	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:15:06	test-plug - Power	0.0
    2023-01-07T13:15:10	test-plug - Ampere	0.0
    2023-01-07T13:15:10	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:15:10	test-plug - Power	0.0
    2023-01-07T13:15:12	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:15:12	test-plug - Power	0.0
    2023-01-07T13:15:16	test-plug - Voltage	226.96109008789062
    2023-01-07T13:15:16	test-plug - Electric Consumption [kWh]	0.0
    2023-01-07T13:15:16	test-plug - Power	0.0

    [...]

You can redirect everything to file in your shell as usual 
(`./esphomelogger ...  > foo.dat`). As the data is tab-separated it can be
imported into other software, easily. E.g. into R:

    library(tidyverse)
    dat <- read_delim("foo.dat", delim="\t")

Let's check if it was read correctly:

    > dat
    # A tibble: 37 × 3
       timestamp           variable                               value
       <dttm>              <chr>                                  <chr>
     1 2023-01-07 13:14:49 test-plug - Status                     True
     2 2023-01-07 13:14:49 test-plug - Electric Consumption [kWh] 0.0
     3 2023-01-07 13:14:49 test-plug - Voltage                    227.00172424316406
     4 2023-01-07 13:14:49 test-plug - Ampere                     0.0
     5 2023-01-07 13:14:49 test-plug - Power                      0.0
     6 2023-01-07 13:14:49 test-plug - Wifi Signal                -59.0
     7 2023-01-07 13:14:49 test-plug - Uptime                     60352.62109375
     8 2023-01-07 13:14:49 test-plug - Switch                     True
     9 2023-01-07 13:14:49 test-plug - IP Address                 192.168.0.55
    10 2023-01-07 13:14:49 test-plug - Wi-Fi SSID                 Foolan
    # … with 27 more rows
    # ℹ Use `print(n = ...)` to see more rows

looking good!

