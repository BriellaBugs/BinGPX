# BinGPX
Minimal binary representation for GPS data focused on file size

# File Structure

* All values are Big Endian

* Fixed-Point sign uses Sign-Magnitude form

Example:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 1 Sign | 7 Integer | 24 fraction              ]`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 0      | 0010100   | 100000000000000000000000 ]` = `20.5`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`[ 1      | 0010100   | 100000000000000000000000 ]` = `-20.5`

## Header

| Field        | Type             | Size      | Description                  |
|--------------|------------------|-----------|------------------------------|
| File Header  | Magic            | `32 bits` | Always `0x42 0x47 0x50 0x58` |
| Version      | Unsigned Integer | `32 bits` | Currently `1036`             |

## For each track

| Field          | Type             | Size                     | Description                                                  |
|----------------|------------------|--------------------------|--------------------------------------------------------------|
| Start of Track | Magic            | `32 bits`                | Always `0x54 0x52 0x48 0x53`                                 |
| TLS            | Unsigned Integer | `2 bits`                 | Track List Size in bits = `(Value+1)*8`                      |
| Track Type     | Unsigned Integer | `2 bits`                 | `0` = Ordered Track, `1` = Unordered Track, `2-3` = Reserved |
| Padding        | Padding          | `4 bits`                 | Padding for byte-alignment                                   |
| Waypoint Count | Unsigned Integer | `TLS bits`               | Number of total waypoints                                    |
| Waypoint Data  |                  | `32 bytes` each waypoint |                                                              |

If Track type is "Unordered" the additional info is added after:

| Field            | Type             | Size                              | Description                                   |
|------------------|------------------|-----------------------------------|-----------------------------------------------|
| Connection Count | Unsigned Integer | `TLS bits`                        | Number of total connections between waypoints |
| Connection Data  |                  | `TLS*2 + 32 bits` each connection |                                               |

## Connection data

| Field         | Type             | Size       | Description |
|---------------|------------------|------------|-------------|
| Point A Index | Unsigned Integer | `TLS bits` |             |
| Point B Index | Unsigned Integer | `TLS bits` |             |
| Width         | Floating-Point   | `32 bits`  | Meters      |

## Waypoint data

| Field       | Type                                                 | Size      | Description                              |
|-------------|------------------------------------------------------|-----------|------------------------------------------|
| Latitude    | Fixed-Point `[ 1 Sign \| 7 Integer \| 24 Fraction ]` | `32 bits` | Degrees `[-90 ≤ 90]`                     |
| Longitude   | Fixed-Point `[ 1 Sign \| 8 Integer \| 23 Fraction ]` | `32 bits` | Degrees `[-180 ≤ 180]`                   |
| Altitude    | Floating-Point                                       | `32 bits` | Meters                                   |
| Heading     | Fixed-Point `[ 9 Integer \| 23 Fraction ]`           | `32 bits` | Degrees `[0 ≤ 360]`                      |
| H. Accuracy | Floating-Point                                       | `32 bits` | Meters                                   |
| V. Accuracy | Floating-Point                                       | `32 bits` | Meters                                   |
| Timestamp   | Unsigned Integer                                     | `64 bits` | Milliseconds since `00:00:00 01/01/1970` |

This is a test of Secret Scanning: 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
