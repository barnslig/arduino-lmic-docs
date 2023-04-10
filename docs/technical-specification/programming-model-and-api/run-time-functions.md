# Run-time Functions

The run-time functions mentioned before are used to control the run-time environment. This includes initialization, scheduling and execution of the run-time jobs.

## `void os_init ()`

Initialize the operating system by calling `os_init_ex(NULL)`.

## `void os_init_ex (const void* pHalData)`

To facilitate use of this library on multiple platforms, the `os_init_ex()` routine takes an arbitrary pointer to platform data. The Arduino LMIC default HAL implementation expects this pointer to be a reference to a C++ `struct lmic_pinmap` object. See README.md for more information.

## `void os_setCallback (osjob_t* job, osjobcb_t cb)`

Prepare an immediately runnable job. This function can be called at any time, including from interrupt handler contexts (e.g. if a new sensor value has become available).

## `void os_setTimedCallback (osjob_t* job, ostime_t time, osjobcb_t cb)`

Schedule a timed job to run at the given timestamp (absolute system time). This function can be called at any time, including from interrupt handler contexts.

## `void os_clearCallback (osjob_t* job)`

Cancel a run-time job. A previously scheduled run-time job is removed from timer and run queues. The job is identified by the address of the job struct. The function has no effect if the specified job is not yet scheduled.

## `void os_runloop ()`

Execute run-time jobs from the timer and from the run queues. This function is the main action dispatcher. It does not return and must be run on the main thread. This routine is normally not used in Arduino environments, as it disables the normal calling of the Arduino `loop()` function.

## `void os_runloop_once ()`

Execute run-time jobs from the timer and from the run queues. This function is just like `os_runloop()`, except that it returns after dispatching the first available job.

## `ostime_t os_getTime ()`

Query absolute system time (in ticks).

## `ostime_t us2osticks (s4_t us)`

Returns the ticks corresponding to the integer value `us`. This may be a function-like macro, so `us` may be evaluated more than once. Any fractional part of the calculation is discarded.

## `ostime_t us2osticksCeil (s4_t us)`

Returns the ticks corresponding to the integer value `us`. This may be a function-like macro, so `us` may be evaluated more than once. If the fractional part of the calculation is non-zero, the result is increased towards positive infinity.

## `ostime_t us2osticksRound (s4_t us)`

Returns the ticks corresponding to the integer value `us`. This may be a function-like macro, so `us` may be evaluated more than once. The result is rounded to the nearest tick.

## `ostime_t ms2osticks (s4_t ms)`

Returns the ticks corresponding to the integer millisecond value ms. This may be a function-like macro, so ms may be evaluated more than once. If the fractional part of the calculation is non-zero, the result is increased towards positive infinity.

## `ostime_t ms2osticksCeil (s4_t ms)`

Returns the ticks corresponding to the integer millisecond value `ms`. This may be a function-like macro, so `ms` may be evaluated more than once.

## `ostime_t ms2osticksRound (s4_t ms)`

Returns the ticks corresponding to the integer millisecond value `ms`. This may be a function-like macro, so `ms` may be evaluated more than once. The result is rounded to the nearest tick.

## `ostime_t sec2osticks (s4_t sec)`

Returns the ticks corresponding to the integer second value `sec`. This may be a function-like macro, so `sec` may be evaluated more than once.

## `S4_t osticks2ms (ostime_t os)`

Returns the milliseconds corresponding to the tick value `os`. This may be a function-like macro, so `os` may be evaluated more than once.

## `S4_t osticks2us (ostime_t os)`

Returns the microseconds corresponding to the tick value `os`. This may be a function-like macro, so `os` may be evaluated more than once.
