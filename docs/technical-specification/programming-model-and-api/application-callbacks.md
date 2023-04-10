# Application callbacks

The LMIC library requires the application to implement a few callback functions. These functions are called by the state engine to query application-specific information and to deliver state events to the application.

Upcalls by name from the LMIC to application code are deprecated and will be removed in future versions of the LMIC. The provisioning APIs (`os_getDevEui`, `os_getDevKey` and `os_getArtEui`) will be replaced by secure element APIs in version 4. The onEvent API will be disabled by default in version 4, and removed in version 5.

## `void os_getDevEui (u1_t* buf)`

The implementation of this callback function has to provide the device EUI and copy it to the given buffer. The device EUI is 8 bytes in length and is stored in little-endian format, that is, least-significant-byte-first (LSBF).

## `void os_getDevKey (u1_t* buf)`

The implementation of this callback function has to provide the device-specific cryptographic application key and copy it to the given buffer. The device-specific application key is a 128-bit AES key (16 bytes in length).

## `void os_getArtEui (u1_t* buf)`

The implementation of this callback function has to provide the application EUI and copy it to the given buffer. The application EUI is 8 bytes in length and is stored in little-endian format, that is, least-significant-byte-first (LSBF).

## `void onEvent (ev_t ev)`

This function, if provided, is called to report LMIC events (such as transmission complete or downlink message received). It is a legacy function. In V3, if this name conflicts with a name in your application, you can disable the use of this name by putting the following in your configuration file:

```c
#define LMIC_ENABLE_onEvent 0
```

Upcalls by name from the LMIC to application code are deprecated and will be removed in future versions of the LMIC. A suitable substitute is available. See the discussion of `LMIC_registerEventCb()`, below.
