# The LMIC Struct

Instead of passing numerous parameters back and forth between API and callback functions, information about the protocol state can be accessed via a global `LMIC` structure as shown below. All fields besides the ones explicitly mentioned below are read-only and should not be modified.

```c
struct lmic_t {
  u1_t      frame[MAX_LEN_FRAME];
  u1_t      dataLen;    // 0 no data or zero length data, >0 byte count of data
  u1_t      dataBeg;    // 0 or start of data (dataBeg-1 is port)

  u1_t      txCnt;
  u1_t      txrxFlags;  // transaction flags (TX-RX combo)

  u1_t      pendTxPort;
  u1_t      pendTxConf; // confirmed data
  u1_t      pendTxLen;
  u1_t      pendTxData[MAX_LEN_PAYLOAD];

  u1_t      bcnChnl;
  u1_t      bcnRxsyms;
  ostime_t  bcnRxtime;
  bcninfo_t bcninfo;    // Last received beacon info

  …
  …
};
```

This document does not describe the full struct in detail since most of the fields of the LMIC struct are used internally only. The most important fields to examine on reception (event `EV_RXCOMPLETE` or `EV_TXCOMPLETE`) are the `txrxFlags` for status information and `frame[]` and `dataLen` / `dataBeg` for the received application payload data. For data transmission the most important fields are `pendTxData[]`, `pendTxLen`, `pendTxPort` and `pendTxConf`, which are used as input to the `LMIC_setTxData()` API function (see 2.5.13).

For the `EV_RXCOMPLETE` and `EV_TXCOMPLETE` events, the `txrxFlags` field should be evaluated. The following flags are defined:

- `TXRX_ACK`: confirmed UP frame was acked (mutually exclusive with `TXRX_NACK`)

- `TXRX_NACK`: confirmed UP frame was not acked (mutually exclusive with `TXRX_ACK`)

- `TXRX_PORT`: a port byte is contained in the received frame at offset LMIC.dataBeg – 1.

- `TXRX_NOPORT`: no port byte is available.

- `TXRX_DNW1`: received in first DOWN slot (mutually exclusive with `TXRX_DNW2`)

- `TXRX_DNW2`: received in second DOWN slot (mutually exclusive with `TXRX_DNW1`)

- `TXRX_PING`: received in a scheduled RX slot

- `TXRX_LENERR`: the transmitted message was abandoned because it was longer than the established data rate.

For the `EV_TXCOMPLETE` event the fields have the following values:

<table>
<colgroup>
<col style="width: 14%" />
<col style="width: 5%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 8%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 8%" />
<col style="width: 16%" />
<col style="width: 16%" />
</colgroup>
<thead>
<tr>
<th rowspan="2"><strong>Received frame</strong></th>
<th colspan="8"><strong>LMIC.txrxFlags</strong></th>
<th rowspan="2"><strong>LMIC.dataLen</strong></th>
<th rowspan="2"><strong>LMIC.dataBeg</strong></th>
</tr>
<tr>
<th><strong>ACK</strong></th>
<th><strong>NACK</strong></th>
<th><strong>PORT</strong></th>
<th><strong>NOPORT</strong></th>
<th><strong>DNW1</strong></th>
<th><strong>DNW2</strong></th>
<th><strong>PING</strong></th>
<th><strong>LENERR</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>nothing</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>0</td>
</tr>
<tr>
<td>empty frame</td>
<td>x</td>
<td>x</td>
<td>0</td>
<td>1</td>
<td>x</td>
<td>x</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>x</td>
</tr>
<tr>
<td>port only</td>
<td>x</td>
<td>x</td>
<td>1</td>
<td>0</td>
<td>x</td>
<td>x</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>x</td>
</tr>
<tr>
<td>port+payload</td>
<td>x</td>
<td>x</td>
<td>1</td>
<td>0</td>
<td>x</td>
<td>x</td>
<td>0</td>
<td>0</td>
<td>x</td>
<td>X</td>
</tr>
<tr>
<td>No message received, transmit message too long</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>x</td>
<td>x</td>
<td>x</td>
<td>1</td>
<td>0</td>
<td>0</td>
</tr>
</tbody>
</table>

For the `EV_RXCOMPLETE` event the fields have the following values:

<table>
<colgroup>
<col style="width: 16%" />
<col style="width: 5%" />
<col style="width: 6%" />
<col style="width: 7%" />
<col style="width: 8%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 6%" />
<col style="width: 16%" />
<col style="width: 16%" />
</colgroup>
<thead>
<tr>
<th rowspan="2"><strong>Received frame</strong></th>
<th colspan="7"><strong>LMIC.txrxFlags</strong></th>
<th rowspan="2"><strong>LMIC.dataLen</strong></th>
<th rowspan="2"><strong>LMIC.dataBeg</strong></th>
</tr>
<tr>
<th><strong>ACK</strong></th>
<th><strong>NACK</strong></th>
<th><strong>PORT</strong></th>
<th><strong>NOPORT</strong></th>
<th><strong>DNW1</strong></th>
<th><strong>DNW2</strong></th>
<th><strong>PING</strong></th>
</tr>
</thead>
<tbody>
<tr>
<td>empty frame</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>x</td>
</tr>
<tr>
<td>port only</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>x</td>
</tr>
<tr>
<td>port+payload</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>0</td>
<td>0</td>
<td>0</td>
<td>1</td>
<td>x</td>
<td>x</td>
</tr>
</tbody>
</table>
