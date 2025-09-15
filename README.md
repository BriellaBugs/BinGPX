# BinGPX
Minimal binary representation for GPS data focused on file size
while still maintaining precision (0.67cm lat and 1.34cm long at the Equator)

# File Structure

* All values are Big Endian

* Files should use the `.bgpx` file extension

* Floats are IEEE 754 Singles

* Fixed-Point sign uses Q-Format

Example:

`Q8.24 ` = `8 bits` for signed integer, `24 bits` for fractional part

`UQ9.23` = `9 bits` for unsigned integer, `23 bits` for fractional part

Both of these examples use `32 bits` in total

## Header

| Field        | Type             | Size      | Description                  |
|--------------|------------------|-----------|------------------------------|
| File Header  | Magic            | `32 bits` | Always `0x42 0x47 0x50 0x58` |
| Version      | Unsigned Integer | `32 bits` | Currently `1057`             |

## For each track

| Field          | Type             | Size                     | Description                                                  |
|----------------|------------------|--------------------------|--------------------------------------------------------------|
| Start of Track | Magic            | `32 bits`                | Always `0x54 0x52 0x4B 0x53`                                 |
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

| Field         | Type                    | Size       | Description |
|---------------|-------------------------|------------|-------------|
| Point A Index | Unsigned Integer        | `TLS bits` |             |
| Point B Index | Unsigned Integer        | `TLS bits` |             |
| Width         | Floating-Point IEEE 754 | `32 bits`  | Meters      |

## Waypoint data

| Field       | Type                    | Size      | Description                              |
|-------------|-------------------------|-----------|------------------------------------------|
| Latitude    | Fixed-Point `Q8.24`     | `32 bits` | Degrees `[-90 ≤ 90]`                     |
| Longitude   | Fixed-Point `Q9.23`     | `32 bits` | Degrees `[-180 ≤ 180]`                   |
| Altitude    | Floating-Point IEEE 754 | `32 bits` | Meters                                   |
| Heading     | Fixed-Point `UQ9.23`    | `32 bits` | Degrees `[0 ≤ 360]`                      |
| H. Accuracy | Floating-Point IEEE 754 | `32 bits` | Meters                                   |
| V. Accuracy | Floating-Point IEEE 754 | `32 bits` | Meters                                   |
| Timestamp   | Unsigned Integer        | `64 bits` | Milliseconds since `00:00:00 01/01/1970` |
