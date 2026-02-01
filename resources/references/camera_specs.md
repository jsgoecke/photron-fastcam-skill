# Photron FASTCAM Camera Specifications

## FASTCAM Mini R5-4K

### Core Specifications

| Parameter | Value |
|-----------|-------|
| Sensor | 9.4 MP CMOS (4096 × 2304 pixels) |
| Maximum Resolution | 4096 × 2304 @ 1,250 fps |
| Maximum Frame Rate | 200,000 fps (at reduced resolution) |
| ISO Sensitivity (Mono) | 4,000 |
| ISO Sensitivity (Color) | 800 |
| Bit Depth | 12-bit |
| Minimum Shutter | 2 μs |
| Interfaces | 1GbE, 10GbE |
| Memory | 8GB / 16GB / 32GB / 64GB |
| IRIG-B | Yes |
| Dimensions | 120 × 120 × 94.4 mm |
| Weight | 2.1 kg |

### Resolution vs Frame Rate

| Resolution | Max FPS | Aspect | Data Rate |
|------------|---------|--------|-----------|
| 4096 × 2304 | 1,250 | 16:9 | ~17.7 GB/s |
| 3840 × 2160 | ~1,400 | 16:9 | ~17.4 GB/s |
| 2048 × 1536 | ~3,000 | 4:3 | ~14.2 GB/s |
| 1920 × 1080 | ~3,500 | 16:9 | ~10.9 GB/s |
| 1280 × 1024 | ~5,500 | 5:4 | ~10.9 GB/s |
| 1280 × 720 | ~7,500 | 16:9 | ~10.4 GB/s |
| 1024 × 1024 | ~8,000 | 1:1 | ~12.6 GB/s |
| 640 × 480 | ~25,000 | 4:3 | ~11.5 GB/s |
| 384 × 352 | ~50,000 | ~1:1 | ~10.1 GB/s |
| 256 × 128 | ~200,000 | 2:1 | ~9.8 GB/s |

### Recording Time (64GB Memory, 12-bit)

| Resolution | FPS | Duration | Notes |
|------------|-----|----------|-------|
| 4096 × 2304 | 100 | ~45 sec | Full 4K |
| 4096 × 2304 | 500 | ~9 sec | High-speed 4K |
| 4096 × 2304 | 1,250 | ~3.6 sec | Max rate 4K |
| 1920 × 1080 | 100 | ~206 sec | Full HD, extended |
| 1920 × 1080 | 500 | ~41 sec | High-speed HD |
| 1920 × 1080 | 1,000 | ~20 sec | Ultra high-speed HD |
| 1280 × 720 | 100 | ~462 sec | HD extended |
| 1280 × 720 | 500 | ~92 sec | HD high-speed |
| 640 × 480 | 1,000 | ~222 sec | VGA extended |

### Recording Time Formula

```
Duration (seconds) = Memory (bytes) / Data Rate (bytes/sec)
Data Rate = H × V × FPS × 1.5 (for 12-bit)
```

## FASTCAM Nova Series

### Nova S12

| Parameter | Value |
|-----------|-------|
| Sensor | 1 MP CMOS (1024 × 1024) |
| Maximum Frame Rate | 12,000 fps @ full resolution |
| Maximum Frame Rate | 480,000 fps @ reduced |
| ISO Sensitivity (Mono) | 50,000 |
| ISO Sensitivity (Color) | 10,000 |
| Memory | Up to 128GB |

### Nova R5-4K

| Parameter | Value |
|-----------|-------|
| Sensor | 9.4 MP CMOS (4096 × 2304) |
| Maximum Frame Rate | 1,250 fps @ 4K |
| Maximum Frame Rate | 200,000 fps @ reduced |
| ISO Sensitivity (Mono) | 4,000 |
| Memory | Up to 128GB |
| Form Factor | Rack-mount |

## FASTCAM SA-Z Series

### SA-Z 2100K

| Parameter | Value |
|-----------|-------|
| Sensor | 1 MP CMOS (1024 × 1024) |
| Maximum Frame Rate | 2,100,000 fps |
| ISO Sensitivity (Mono) | 50,000 |
| Memory | Up to 256GB |

## Lens Mounts

| Mount | Cameras | Notes |
|-------|---------|-------|
| F-mount (Nikon) | Mini R5, Nova series | Standard |
| C-mount | All models | With adapter |
| EF-mount (Canon) | Available | With adapter |

## Trigger Options

| Type | Connection | Notes |
|------|------------|-------|
| External TTL | BNC | 5V TTL signal |
| Software | Ethernet | PDC_TriggerIn() |
| IRIG-B | BNC | Time synchronization |
| External Sync | BNC | Multi-camera sync |
| GPIO | DB-25 | General purpose I/O |

## Trigger Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| Start | Trigger starts recording | Predictable events |
| Center | Trigger at midpoint | Events with lead-up |
| End | Trigger ends recording | Pre-trigger capture |
| Random | Multiple triggers | Multiple events |
| Manual | Software trigger | Remote control |

## Memory Partition Modes

| Mode | Description | Use Case |
|------|-------------|----------|
| Single | All memory as one block | Single long event |
| Partition | Multiple segments | Multiple events |
| Random Loop | Loop recording | Unknown timing |
| Burst | Triggered bursts | Repetitive events |

## Interface Specifications

### Gigabit Ethernet (1GbE)
- Standard: 1000BASE-T
- Connector: RJ-45
- Cable: CAT5e or better
- Max Download: ~100 MB/s

### 10 Gigabit Ethernet (10GbE)
- Standard: 10GBASE-T
- Connector: RJ-45
- Cable: CAT6a or better
- Max Download: ~1 GB/s

### FASTDrive
- Interface: SSD bay
- Transfer: 64GB in ~1 minute
- Media: Removable SSD

## Environmental Specifications

| Parameter | Value |
|-----------|-------|
| Operating Temp | 0°C to 40°C |
| Storage Temp | -20°C to 60°C |
| Humidity | 20% to 80% RH |
| Vibration | 2G (operating) |
| Shock | 30G (11ms) |

## Power Requirements

| Parameter | Mini R5 | Nova S12 |
|-----------|---------|----------|
| Voltage | 20-28 VDC | 20-28 VDC |
| Power | ~60W | ~80W |
| Connector | 4-pin XLR | 4-pin XLR |

## Synchronization

### IRIG-B
- Input: IRIG-B (DC) or IRIG-B (AC)
- Accuracy: ±100 μs
- Use: GPS time correlation

### Multi-Camera Sync
- Method: External sync signal
- Accuracy: ±1 frame
- Daisy-chain: Up to 16 cameras

## Sensitivity Comparison

| Camera | Mono ISO | Color ISO | Notes |
|--------|----------|-----------|-------|
| Mini R5-4K | 4,000 | 800 | 4K resolution |
| Nova S12 | 50,000 | 10,000 | Best low-light |
| SA-Z | 50,000 | 10,000 | Ultra high-speed |

## ISO and Low-Light Performance

### ISO 12232 Limitations
- ISO rating alone doesn't indicate noise performance
- Key metrics: Temporal Dark Noise, Absolute Sensitivity Threshold
- Higher ISO often means added gain (lower SNR)

### Low-Light Recommendations
1. Use monochrome sensor (5x more sensitive than color)
2. Lower frame rate = more light per frame
3. Longer shutter time when motion blur acceptable
4. Fast lens (f/1.4 - f/2.8)
5. Consider image intensifier for extreme low-light

### Dynamic Range Considerations
- 12-bit sensor provides 72dB theoretical dynamic range
- Higher ISO reduces effective dynamic range
- RAW capture preserves maximum flexibility

## File Formats

| Format | Extension | Notes |
|--------|-----------|-------|
| MRAW | .mraw | Native Photron format |
| AVI | .avi | Uncompressed video |
| TIFF | .tif | Individual frames |
| CIHX | .cihx | Camera info header |
| MCI | .mci | Multi-frame container |

## PFV Software Versions

| Version | Key Features |
|---------|--------------|
| PFV 4.x | Current, 4K support, SDK 4.3 |
| PFV 3.x | Legacy, stable |

## Accessories

| Accessory | Purpose |
|-----------|---------|
| FASTDrive SSD | Fast data offload |
| Battery Pack | Field operation |
| Fiber Optic Link | Long-distance connection |
| Image Intensifier | Low-light amplification |
| Lens Adapter Kit | Mount conversion |
