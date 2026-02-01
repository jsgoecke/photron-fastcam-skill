# Photron FASTCAM Skill

A Claude Code skill providing expert guidance for Photron high-speed cameras and SDK programming.

## Overview

This skill transforms Claude into a Photron FASTCAM expert, offering assistance with:

- **Camera Programming** - PDCLIB SDK, PyHSCam, photron-linux-sdk integration
- **Performance Calculations** - Frame rates, recording times, memory requirements
- **Configuration Optimization** - Low-light imaging, high-speed capture, scientific applications
- **Direct Frame Streaming** - Bypassing PFV GUI for real-time processing
- **Troubleshooting** - Connectivity issues, download speeds, data unpacking

## Supported Cameras

| Series | Models | Key Specs |
|--------|--------|-----------|
| **Mini R5-4K** | Mini R5-4K | 4K @ 1,250 fps, 200,000 fps max, ISO 4000 (mono) |
| **Nova** | Nova S12, Nova R5-4K | Up to 480,000 fps, ISO 50,000 (mono), 128GB memory |
| **SA-Z** | SA-Z 2100K | 2.1M fps, ISO 50,000, 256GB memory |

## Installation

### As a Claude Code Skill

1. Copy the `photron-fastcam.skill` file to your Claude Code skills directory:
   ```bash
   cp photron-fastcam.skill ~/.claude/skills/
   ```

2. Or build and install from the resources directory:
   ```bash
   cd photron-fastcam/resources
   zip -r ../photron-fastcam.skill .
   cp ../photron-fastcam.skill ~/.claude/skills/
   ```

### Directory Structure

```
photron-fastcam/
├── README.md                         # This file
├── photron-fastcam.skill             # Packaged skill file
└── resources/
    ├── SKILL.md                      # Main skill definition and quick reference
    ├── references/
    │   ├── camera_specs.md           # Detailed camera specifications
    │   ├── sdk_reference.md          # PDCLIB API documentation
    │   └── api_reference.md          # Additional API reference
    ├── scripts/
    │   └── example.py                # Example helper script
    └── assets/
        └── example_asset.txt         # Asset placeholder
```

## Quick Reference

### Recording Time Calculation

```
Duration = Memory / (H x V x FPS x 1.5)
```

**Example:** 64GB at 1920x1080 @ 100fps = ~206 seconds

### Resolution vs Recording Time (64GB, Mini R5-4K)

| Resolution | Max FPS | Duration @ 1000fps |
|------------|---------|-------------------|
| 4096x2304 | 1,250 | ~3.6 sec @ max |
| 1920x1080 | ~3,500 | ~20 sec |
| 1280x720 | ~7,500 | ~92 sec @ 500fps |
| 640x480 | ~25,000 | ~222 sec |

### SDK Quick Start (C)

```c
PDC_Init(&err);
PDC_DetectDevice(PDC_INTTYPE_G_ETHER, &num, 10, info, &err);
PDC_OpenDevice(&info[0], &dev, &err);
PDC_SetRecordRate(dev, 1, 1000, &err);
PDC_GetLiveImageData(dev, 1, 8, buffer, &err);  // Stream frames
PDC_CloseDevice(dev, &err);
PDC_Close(&err);
```

### SDK Quick Start (Python with PyHSCam)

```python
import PyHSCam

PyHSCam.init()
cameras = PyHSCam.detect()
cam = PyHSCam.open(cameras[0])

frame = cam.get_live_image()
cam.set_rate(1000)  # 1000 fps
cam.set_resolution(1920, 1080)

cam.close()
PyHSCam.close()
```

## Key Features

### Direct Frame Streaming

Stream frames without PFV overhead:

| Platform | Solution | Notes |
|----------|----------|-------|
| Windows (C/C++) | PDCLIB directly | Lowest latency |
| Windows (Python) | PyHSCam | Requires PDCLIB.dll |
| Linux | photron-linux-sdk | Experimental, FastAPI on port 5000 |

### Network Configuration

```
Camera IP: 192.168.0.10 (default)
PC NIC: 192.168.0.x (same subnet)
Jumbo frames: Enable (9000 bytes)
Firewall: Allow application through
```

**Important:** Direct connection to 1GbE/10GbE NIC required. No 100BASE-TX switches.

### Trigger Modes

| Application | Mode | Pre/Post Split |
|-------------|------|----------------|
| Unpredictable timing | Random/Endless | 80% pre / 20% post |
| Known start time | Start | 0% pre / 100% post |
| Capture aftermath | End | 100% pre / 0% post |
| Event with lead-up | Center | 50% pre / 50% post |

### Low-Light Optimization

1. Use monochrome sensor (5x more sensitive than color)
2. Lower frame rate for more integration time
3. Maximize shutter duration (balance with motion blur)
4. Fast lens (f/1.4 - f/2.8)
5. 12-bit RAW capture for post-processing flexibility

### 12-bit Data Unpacking

Two pixels packed in 3 bytes:
```c
pixel1 = (byte0 << 4) | (byte1 >> 4);
pixel2 = ((byte1 & 0x0F) << 8) | byte2;
```

## Troubleshooting

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
3. Check buffer size matches resolution x bit depth

## Integration Support

| Platform | Library/Driver |
|----------|---------------|
| MATLAB | Official MEX interface (v4.4.0.0) |
| LabVIEW | Official VI library (v4.4.0.0) |
| EPICS | ADPhotron areaDetector driver |
| Python (Win) | PyHSCam wrapper |
| Python (Linux) | photron-linux-sdk |

## Resources

- **Photron Website:** [photron.com](https://photron.com)
- **PFV Download:** [photron.com/pfv/](https://photron.com/pfv/)
- **PyHSCam:** [github.com/ion201/PyHSCam](https://github.com/ion201/PyHSCam)
- **Linux SDK:** [github.com/felix-engelmann/photron-linux-sdk](https://github.com/felix-engelmann/photron-linux-sdk)
- **EPICS Driver:** [github.com/kmpeters/ADPhotron](https://github.com/kmpeters/ADPhotron)
- **MRAW Reader:** [github.com/ladisk/pyMRAW](https://github.com/ladisk/pyMRAW)

## Contact

- **Email:** image@photron.com
- **Phone:** 800-585-2129

## License

This skill is provided as-is for use with Claude Code. Camera SDK and software are subject to Photron's licensing terms.
