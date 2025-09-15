# BinGPX
Minimal binary representation for GPS data focused on file size

# File Structure

* All values are Big Endian

* Types are always followed by their size in bits, never bytes

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Example: `uint2` is 2 bits, not 2 bytes

* `fixed32` sign uses Sign-Magnitude form

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Example:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 1 sign | 7 integer | 24 fraction ]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`0 0010100 100000000000000000000000` = `20.5`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`1 0010100 100000000000000000000000` = `-20.5`

## Header

Magic Number `0x42 0x47 0x50 0x58`

`uint32` Format Version `1023`

## For each track

"Start of Track" Magic Number `0x54 0x52 0x48 0x53`

`uint2` Track list size

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`TLS` = `(Value+1)*8`

`2bit` Track type:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`00` = Ordered track

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`01` = Unordered track

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`10 - 11` = Reserved

`4bit` Padding for byte-alignment

`uintTLS` Waypoint count

1 x `Waypoint data` for Waypoint count

### If Track type is 01

`uintTLS` Connection count

1 x `Connection data` for Connection count

## Connection data

`uintTLS` Point A Index

`uintTLS` Point B Index

`float32` width in meters

## Waypoint data

`fixed32` latitude in degrees `[-90 ≤ 90]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 1 sign | 7 integer | 24 fraction ]`

`fixed32` longitude in degrees `[-180 ≤ 180]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 1 sign | 8 integer | 23 fraction ]`

`float32` altitude in meters

`fixed32` heading in degrees `[0 ≤ 360]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 9 integer | 23 fraction ]`

`float32` horizontal accuracy in meters

`float32` vertical accuracy in meters

`uint64` UNIX Timestamp in Milliseconds

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Milliseconds since `00:00:00 - 01/01/1970`
