# ESPhomelogger
Log entity states of ESPhome devices without HASSIO

Devices running ESPhome firmware can easily be integrated into Homeassistant but 
sometimes, I want to use them standalone or access them outside of HASSIO for other reasons. 

This little script uses the ESPhome native API to get the state of various entities implemented in the device and 
write the data to STDOUT as a tab separated table (TSV) for easy use in downstream analysis.
