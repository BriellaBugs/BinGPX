# BinGPX
Minimal binary representation for GPS data focused on file size
while still maintaining precision (0.67cm lat and 1.34cm long at the Equator)

# File Structure

* All values are Big Endian

* Files should use the `.bgpx` file extension

* Floats are IEEE 754 Singles

* Fixed-Point sign uses Q-Format

* No byte-padding, values are the exact size as stated

Example:

`Q8.24 ` = `8 bits` for signed integer, `24 bits` for fractional part

`UQ9.23` = `9 bits` for unsigned integer, `23 bits` for fractional part

Both of these examples use `32 bits` in total

## Header

| Field        | Type             | Size      | Description                  |
|--------------|------------------|-----------|------------------------------|
| File Header  | Magic            | `32 bits` | Always `0x42 0x47 0x50 0x58` |
| Version      | Unsigned Integer | `32 bits` | Currently `1093`             |

## For each track

| Field          | Type             | Size                     | Description                                                  |
|----------------|------------------|--------------------------|--------------------------------------------------------------|
| Start of Track | Magic            | `32 bits`                | Always `0x54 0x52 0x4B 0x53`                                 |
| TLS            | Unsigned Integer | `2 bits`                 | Track List Size in bits = `(Value+1)*8`                      |
| Track Type     | Unsigned Integer | `2 bits`                 | `0` = Ordered Track, `1` = Unordered Track, `2-3` = Reserved |
| Track Color    | Unsigned Integer | `4 bits`                 | Color Index `[0 ≤ 15]`                                       |
| Waypoint Count | Unsigned Integer | `TLS bits`               | Number of total waypoints                                    |
| Waypoint Data  |                  | `32 bytes` each waypoint |                                                              |

If Track type is "Unordered" then additional info is added, "Ordered" Track Types don't include this:

| Field            | Type             | Size                    | Description                                   |
|------------------|------------------|-------------------------|-----------------------------------------------|
| Connection Count | Unsigned Integer | `TLS bits`              | Number of total connections between waypoints |
| Connection Data  |                  | `TLS*2` each connection |                                               |

## Connection data

| Field         | Type                    | Size       | Description |
|---------------|-------------------------|------------|-------------|
| Point A Index | Unsigned Integer        | `TLS bits` |             |
| Point B Index | Unsigned Integer        | `TLS bits` |             |

## Waypoint data

| Field       | Type                    | Size      | Description                                       |
|-------------|-------------------------|-----------|---------------------------------------------------|
| Latitude\*  | Fixed-Point `Q8.24`     | `32 bits` | Degrees `[-90 ≤ 90]`                              |
| Longitude\* | Fixed-Point `Q9.23`     | `32 bits` | Degrees `[-180 ≤ 180]`                            |
| Altitude    | Floating-Point IEEE 754 | `32 bits` | Meters                                            |
| Heading     | Fixed-Point `UQ9.23`    | `32 bits` | Degrees `[0 ≤ 360]`                               |
| H. Accuracy | Floating-Point IEEE 754 | `32 bits` | Meters                                            |
| V. Accuracy | Floating-Point IEEE 754 | `32 bits` | Meters                                            |
| Timestamp\* | Unsigned Integer        | `64 bits` | Milliseconds since `00:00:00 01/01/1970` UTC Time |

>"\*" = Required
>
>Optional Fixed-Point fields are omitted when equal to `0xFFFFFFFF`'s
>
>Optional Floating-Point fields are omitted when `NaN`

## Color index

| Index | Name              | Hex       | Preview                                                                                                       |
|-------|-------------------|-----------|---------------------------------------------------------------------------------------------------------------|
| 0     | White             | `#FFFFFF` | ![White](https://img.shields.io/badge/%20%20-%23FFFFFF?style=for-the-badge&logo=&logoColor=white)             |
| 1     | Yellow            | `#FCF404` | ![Yellow](https://img.shields.io/badge/%20%20-%23FCF404?style=for-the-badge&logo=&logoColor=white)            |
| 2     | Orange            | `#FF6404` | ![Orange](https://img.shields.io/badge/%20%20-%23FF6404?style=for-the-badge&logo=&logoColor=white)            |
| 3     | Red               | `#DC0808` | ![Red](https://img.shields.io/badge/%20%20-%23DC0808?style=for-the-badge&logo=&logoColor=white)               |
| 4     | Pink              | `#F00884` | ![Pink](https://img.shields.io/badge/%20%20-%23F00884?style=for-the-badge&logo=&logoColor=white)              |
| 5     | Purple            | `#4800A4` | ![Purple](https://img.shields.io/badge/%20%20-%234800A4?style=for-the-badge&logo=&logoColor=white)            |
| 6     | Blue              | `#0000D4` | ![Blue](https://img.shields.io/badge/%20%20-%230000D4?style=for-the-badge&logo=&logoColor=white)              |
| 7     | Light Blue        | `#00ACE8` | ![Light Blue](https://img.shields.io/badge/%20%20-%2300ACE8?style=for-the-badge&logo=&logoColor=white)        |
| 8     | Green             | `#20B814` | ![Green](https://img.shields.io/badge/%20%20-%2320B814?style=for-the-badge&logo=&logoColor=white)             |
| 9     | Dark Green        | `#006410` | ![Dark Green](https://img.shields.io/badge/%20%20-%23006410?style=for-the-badge&logo=&logoColor=white)        |
| 10    | Brown             | `#582C04` | ![Brown](https://img.shields.io/badge/%20%20-%23582C04?style=for-the-badge&logo=&logoColor=white)             |
| 11    | Metallic Sunburst | `#907038` | ![Metallic Sunburst](https://img.shields.io/badge/%20%20-%23907038?style=for-the-badge&logo=&logoColor=white) |
| 12    | Light Gray        | `#C0C0C0` | ![Light Gray](https://img.shields.io/badge/%20%20-%23C0C0C0?style=for-the-badge&logo=&logoColor=white)        |
| 13    | Gray              | `#808080` | ![Gray](https://img.shields.io/badge/%20%20-%23808080?style=for-the-badge&logo=&logoColor=white)              |
| 14    | Dark Gray         | `#404040` | ![Dark Gray](https://img.shields.io/badge/%20%20-%23404040?style=for-the-badge&logo=&logoColor=white)         |
| 15    | Black             | `#000000` | ![Black](https://img.shields.io/badge/%20%20-%23000000?style=for-the-badge&logo=&logoColor=white)             |
