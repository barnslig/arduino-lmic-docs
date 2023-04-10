# Introduction

The LMIC library can be accessed via a set of API functions, run-time functions, callback functions, and a global LMIC data structure. The interface is defined in a single header file “`lmic.h`” which all applications should include.

```c
#include "lmic.h"
```

The library version follows Semantic Versioning 2.0.0 (<https://semver.org/>). A single symbol represents the version. Bits 31..24 represent the major version, bits 23..15 the minor version, and bits 15..8 represent the patch. Bits 7..0 are used for representing the pre-release, called “LOCAL” for historical reasons.

A function-like macro, `ARDUINO_LMIC_VERSION_CALC()`, is used to construct version numbers.

```c
#define ARDUINO_LMIC_VERSION ARDUINO_LMIC_VERSION_CALC(2, 3, 2, 0)
```

For convenience, the library supplies function-like macros `ARDUINO_LMIC_VERSION_GET_MAJOR()`, `ARDUINO_LMIC_VERSION_GET_MINOR()`, `ARDUINO_LMIC_VERSION_GET_PATCH()`, and `ARDUINO_LMIC_­VERSION_­GET_LOCAL()`, which extract the appropriate field from a version number.

Version numbers are represented naively – the four fields are simply packed together into a 32-bit word. This makes them easy to display, but hard to compare. Pre-releases are numbered higher than releases, but compare less. Several macros are provided to make it easy to compare versions numbers. `ARDUINO_LMIC_VERSION_TO_ORDINAL()` converts a version number computed by `ARDUINO_LMIC_VERSION_CALC()` into an integer that can be compared using the normal C comparison operators. `ARDUINO_LMIC_VERSION_COMPARE_LT(v1, v2)` compares two version numbers and returns non-zero if v1 is less than v2 (after converting both to ordinals). `ARDUINO_LMIC_VERSION_COMPARE_LE(v1, v2)`, `ARDUINO_LMIC_VERSION_COMPARE_GT(v1, v2)`, and `ARDUINO_LMIC_VERSION_COMPARE_GE(v1, v2)` test for less-than-or-equal, greater-than, or greater-than-or-equal relationships.

To identify the original version of the IBM LMIC library two constants are defined in this header file.

```c
#define LMIC_VERSION_MAJOR 1
#define LMIC_VERSION_MINOR 6
```

These version strings identify the base version of the library, and will not change.

## Programming Model

The LMIC library offers a simple event-based programming model where all protocol events are dispatched to the application’s `onEvent()` callback function (see 2.3.4). In order to free the application of details like timings or interrupts, the library has a built-in run-time environment to take care of timer queues and job management.

### Application jobs

In this model all application code is run in so-called jobs which are executed on the main thread by the run-time scheduler function `os_runloop()` (see 2.2.6). These application jobs are coded as normal C functions and can be managed using the run-time functions described in section 2.1.3. For the job management an additional per job control struct `osjob_t` is required which identifies the job and stores context information. **Jobs must not be long-running** in order to ensure seamless operation**!** They should only update state and schedule actions, which will trigger new job or event callbacks.

### Main event loop

All an application must do is to initialize the run-time environment using the `os_init()` or `os_init_ex()` function and then periodically call the job scheduler function `os_runloop_once()`. In order to bootstrap protocol actions and generate events, an initial job needs to be set up. Therefore, a startup job is scheduled using the `os_setCallback()` function.

```c
osjob_t initjob;

void setup () {
  // initialize run-time env
  os_init();
  // setup initial job
  os_setCallback(&initjob, initfunc);
}

void loop () {
  // execute scheduled jobs and events
  os_runloop_once();
}
```

The startup code shown in the `initfunc()` function below initializes the MAC and starts joining the network.

```c
// initial job
static void initfunc (osjob_t* j) {
  // reset MAC state
  LMIC_reset();
  // start joining
  LMIC_startJoining();
  // init done - onEvent() callback will be invoked...
}
```

The `initfunc()` function will return immediately, and the `onEvent()` callback function will be invoked by the scheduler later on for the events `EV_JOINING`, `EV_JOINED` or `EV_JOIN_FAILED`.

### OS time

The LMIC uses values of the type `ostime_t` to represent time in ticks. The rate of these ticks defaults to 32768 ticks per second, but may be configured at compile time to any value between 10000 ticks per second and 64516 ticks per second.

In general, one tick is not an integral number of microseconds or milliseconds. Convenience functions are provided for switching back and forth.

```c
typedef int32_t ostime_t;
```

Note that this is a _signed integer_ value; care must be taken when computing differences to avoid being fooled by overflow. OS time starts at zero, and increments uniformly to `INT32_MAX`; it then wraps to `INT32_MIN` and increments uniformly up to zero, and repeats. Rather than comparing two `ostime_t` values, recommended practice is to subtract them and see if the result is positive or negative.
