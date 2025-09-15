# BinGPX
Minimal binary representation for GPS data focused on file size

# File Structure

* All values are Big Endian

* Types are always followed by their size in bits, never bytes

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Example: `uint2` is 2 bits, not 2 bytes

* `fixed32` sign uses Sign-Magnitude form

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Example:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 1 Sign | 7 Integer | 24 fraction ]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`0 0010100 100000000000000000000000` = `20.5`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`1 0010100 100000000000000000000000` = `-20.5`

## Header

| Field        | Type         | Size    | Description           |
|--------------|--------------|---------|-----------------------|
| File Header  | Magic        | 32 bits | `0x42 0x47 0x50 0x58` |
| Version      | Unsigned Int | 32 bits | Currently `1025`      |

## For each track

| Field          | Type         | Size                   | Description                                                  |
|----------------|--------------|------------------------|--------------------------------------------------------------|
| Start of Track | Magic        | 32 bits                | `0x54 0x52 0x48 0x53`                                        |
| TLS            | Unsigned Int | 2 bits                 | Track List Size in bits = `(Value+1)*8`                      |
| Track Type     | Unsigned Int | 2 bits                 | `0` = Ordered Track, `1` = Unordered Track, `2-3` = Reserved |
|                | Padding      | 4 bits                 | Padding for byte-alignment                                   |
| Waypoint Count | Unsigned Int | TLS bits               | Number of total waypoints                                    |
| Waypoint Data  |              | 32 bytes each waypoint |                                                              |

### If Track type is 01

| Field            | Type         | Size                                  | Description                                   |
|------------------|--------------|---------------------------------------|-----------------------------------------------|
| Connection Count | Unsigned Int | TLS bits                              | Number of total connections between waypoints |
| Connection Data  |              | `TLS*2 + 32` bits for each connection |                                               |

## Connection data

| Field         | Type           | Size     | Description |
|---------------|----------------|----------|-------------|
| Point A Index | Unsigned Int   | TLS bits |             |
| Point B Index | Unsigned Int   | TLS bits |             |
| Width         | Floating-Point | 32 bits  | Meters      |

## Waypoint data

| Field       | Type                                                 | Size    | Description                              |
|-------------|------------------------------------------------------|---------|------------------------------------------|
| Latitude    | Fixed-Point `[ 1 Sign \| 7 Integer \| 24 Fraction ]` | 32 bits | Degrees `[-90 ≤ 90]`                     |
| Longitude   | Fixed-Point `[ 1 Sign \| 8 Integer \| 23 Fraction ]` | 32 bits | Degrees `[-180 ≤ 180]`                   |
| Altitude    | Floating-Point                                       | 32 bits | Meters                                   |
| Heading     | Fixed-Point `[ 9 Integer \| 23 Fraction ]`           | 32 bits | Degrees `[0 ≤ 360]`                      |
| H. Accuracy | Floating-Point                                       | 32 bits | Meters                                   |
| V. Accuracy | Floating-Point                                       | 32 bits | Meters                                   |
| Timestamp   | Unsigned Integer                                     | 64 bits | Milliseconds since `00:00:00 01/01/1970` |
