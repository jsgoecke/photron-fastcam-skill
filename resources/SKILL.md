---
name: photron-fastcam
description: |
  Expert system for Photron FASTCAM high-speed cameras and SDK (PDCLIB). Use when:
  - Programming or integrating Photron cameras (Mini R5-4K, Nova, SA-Z series)
  - Working with PDCLIB SDK, PyHSCam, or photron-linux-sdk
  - Calculating frame rates, recording times, or memory requirements
  - Configuring camera settings for specific applications (low-light, high-speed, scientific imaging)
  - Bypassing PFV GUI for direct frame streaming
  - Troubleshooting camera connectivity or performance issues
  - Integrating with MATLAB, LabVIEW, EPICS, or custom applications
  Triggers: Photron, FASTCAM, PDCLIB, high-speed camera SDK, PyHSCam, frame streaming, trigger configuration
---

# Photron FASTCAM Expert

Expert guidance for Photron high-speed cameras and SDK programming.

## Quick Reference

### Recording Time Calculation

```
Duration = Memory / (H × V × FPS × 1.5)
```

Example: 64GB at 1920×1080 @ 100fps = 64GB / (1920×1080×100×1.5) = ~206 seconds

### Key Frame Rate/Resolution Combos (Mini R5-4K)

| Resolution | Max FPS | 64GB Duration |
|------------|---------|---------------|
| 4096×2304 | 1,250 | 3.6 sec |
| 1920×1080 | ~3,500 | 20 sec @ 1000fps |
| 1280×720 | ~7,500 | 92 sec @ 500fps |
| 640×480 | ~25,000 | 222 sec @ 1000fps |

### SDK Quick Start

```c
PDC_Init(&err);
PDC_DetectDevice(PDC_INTTYPE_G_ETHER, &num, 10, info, &err);
PDC_OpenDevice(&info[0], &dev, &err);
PDC_SetRecordRate(dev, 1, 1000, &err);
PDC_GetLiveImageData(dev, 1, 8, buffer, &err);  // Stream frames
PDC_CloseDevice(dev, &err);
PDC_Close(&err);
```

## Core Workflows

### 1. Direct Frame Streaming (Bypass PFV)

To stream frames without PFV overhead:

1. **Windows (Official SDK)**
   - Use PDCLIB directly via C/C++ or Python wrapper
   - Call `PDC_GetLiveImageData()` in tight loop
   - Process frames in separate thread
   - See [SDK Reference](references/sdk_reference.md) for full API

2. **Windows (Python)**
   - Use PyHSCam wrapper (github.com/ion201/PyHSCam)
   - Requires PDCLIB.dll in project directory
   - Add Python to Windows Firewall exceptions

3. **Linux (Experimental)**
   - Use photron-linux-sdk (github.com/felix-engelmann/photron-linux-sdk)
   - Camera on UDP port 2000
   - FastAPI exposes frames on HTTP port 5000
   - Limited parameter control

### 2. Low-Light/Night Imaging

For optimal low-light performance:

1. **Use monochrome sensor** (ISO 4000 vs ISO 800 color = 5× more sensitive)
2. **Lower frame rate** when possible (more integration time)
3. **Maximize shutter duration** (balance with motion blur)
4. **Fast lens** (f/1.4 - f/2.8)
5. **12-bit RAW capture** for post-processing flexibility
6. Consider **image intensifier** for extreme low-light

### 3. Maximizing Recording Duration

Priority order:

1. **Upgrade memory** to 64GB maximum
2. **Reduce resolution** (1080p gives 4× longer than 4K)
3. **Lower frame rate** (100fps vs 1000fps = 10× longer)
4. **Use 10GbE** for faster segment download
5. **Memory segmentation** - record to one partition while downloading another
6. **FASTDrive** - 64GB offload in ~1 minute

### 4. Network Setup

```
Camera IP: 192.168.0.10 (default)
PC NIC: 192.168.0.x (same subnet)
Jumbo frames: Enable (9000 bytes)
Firewall: Allow application through
```

**Critical**: Direct connection to 1GbE/10GbE NIC required. No 100BASE-TX switches.

### 5. Multi-Camera Synchronization

1. Configure master camera with external sync output
2. Connect sync signal to slave cameras
3. Use IRIG-B for GPS time correlation
4. Software trigger via `PDC_TriggerIn()` for simultaneous capture

## Detailed References

- **SDK API Functions**: See [references/sdk_reference.md](references/sdk_reference.md)
- **Camera Specifications**: See [references/camera_specs.md](references/camera_specs.md)

## Common Issues

### Camera Not Detected

1. Check IP subnet match (camera: 192.168.0.10)
2. Disable firewall temporarily to test
3. Use direct connection (no switch)
4. Verify 1GbE/10GbE NIC (not 100Mbps)

### Slow Download Speeds

1. Enable jumbo frames on NIC
2. Use 10GbE if available
3. Disable NIC power saving
4. Use dedicated NIC (not shared)

### Live Image Errors

1. Call `PDC_EraseCachedCorrectionData()` before connecting
2. Verify camera status is `PDC_STATUS_LIVE`
3. Check buffer size matches resolution × bit depth

### 12-bit Data Unpacking

Two pixels packed in 3 bytes:
```c
pixel1 = (byte0 << 4) | (byte1 >> 4);
pixel2 = ((byte1 & 0x0F) << 8) | byte2;
```

## Trigger Configuration

| Application | Mode | Pre/Post |
|-------------|------|----------|
| Unpredictable timing | Random/Endless | 80% pre / 20% post |
| Known start time | Start | 0% pre / 100% post |
| Capture aftermath | End | 100% pre / 0% post |
| Event with lead-up | Center | 50% pre / 50% post |

## Contact & Resources

- **Photron**: photron.com, image@photron.com, 800-585-2129
- **PFV Download**: photron.com/pfv/
- **PyHSCam**: github.com/ion201/PyHSCam
- **Linux SDK**: github.com/felix-engelmann/photron-linux-sdk
- **EPICS Driver**: github.com/kmpeters/ADPhotron
- **MRAW Reader**: github.com/ladisk/pyMRAW
