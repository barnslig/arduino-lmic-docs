# Release History

The Arduino LMIC release history is in the README.md file at <https://github.com/mcci-catena/arduino-lmic>.

## IBM Release History

### 1.6 - Jul 2016

Changed license to BSD. Included modem application (see examples/modem and LMIC-Modem.pdf). Added STM32 hardware drivers and Blipper board-specific peripheral code.

### 1.5 - May 2015

Bug fixes and documentation update.

### 1.4 - March 2015

Changed API: port indicator flag in `LMIC.txrxFlags` has been inverted (now `TXRX_PORT`, previously `TXRX_NOPORT`). Internal bug fixes. Document formatting.

### 1.2 - February 2015

Added APIs `LMIC_setupBand()`, `LMIC_setupChannel()`, `LMIC_disableChannel()`, `LMIC_setLinkCheckMode()`. Minor internal fixes.

### 1.1 - January 2015

Added API `LMIC_setSession()`. Minor internal fixes.

### 1.0 - November 2014

Initial version.
