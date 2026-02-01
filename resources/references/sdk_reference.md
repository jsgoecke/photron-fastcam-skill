# Photron SDK Reference (PDCLIB)

## Overview

The Photron SDK uses PDCLIB.DLL as the core library for camera control. Current version: 4.3.0.0.

## SDK Structure

```
project/
├── PDCLIB.dll          # Core 32-bit runtime library (required)
├── PDCLIB.lib          # Build-time linking library
├── inc/
│   ├── PDCFUNC.h       # API function signatures
│   ├── PDCSTR.h        # Structure definitions
│   └── PDCVALUE.h      # Constants and enumerations
└── dll/
    ├── D512PCI.dll
    ├── D1024PCI.dll
    ├── DAPX.dll
    └── [other supporting DLLs]
```

## Core API Functions

### Initialization

```c
// Initialize the SDK - must be called first
unsigned long PDC_Init(unsigned long *pErrorCode);

// Close the SDK - call when done
unsigned long PDC_Close(unsigned long *pErrorCode);
```

### Device Discovery and Connection

```c
// Search for cameras on network
unsigned long PDC_DetectDevice(
    unsigned long interfaceType,      // PDC_INTTYPE_G_ETHER for Gigabit Ethernet
    unsigned long *pDetectNum,        // Number of cameras found
    unsigned long detectNumMax,       // Max cameras to detect
    PDC_DETECT_NUM_INFO *pDetectInfo, // Array of detected camera info
    unsigned long *pErrorCode
);

// Open connection to camera
unsigned long PDC_OpenDevice(
    PDC_DETECT_NUM_INFO *pDetectInfo,
    unsigned long *pDeviceNo,         // Returns device handle
    unsigned long *pErrorCode
);

// Close camera connection
unsigned long PDC_CloseDevice(
    unsigned long deviceNo,
    unsigned long *pErrorCode
);
```

### Camera Configuration

```c
// Set recording rate (fps)
unsigned long PDC_SetRecordRate(
    unsigned long deviceNo,
    unsigned long childNo,            // 1 for single-head cameras
    unsigned long rate,               // Frames per second
    unsigned long *pErrorCode
);

// Set resolution
unsigned long PDC_SetResolution(
    unsigned long deviceNo,
    unsigned long childNo,
    unsigned long width,
    unsigned long height,
    unsigned long *pErrorCode
);

// Set shutter speed
unsigned long PDC_SetShutterSpeed(
    unsigned long deviceNo,
    unsigned long childNo,
    unsigned long shutterSpeed,       // In nanoseconds or preset value
    unsigned long *pErrorCode
);

// Set trigger mode
unsigned long PDC_SetTriggerMode(
    unsigned long deviceNo,
    unsigned long mode,               // PDC_TRIGGER_* constants
    unsigned long *pErrorCode
);

// Set recording mode
unsigned long PDC_SetRecordingMode(
    unsigned long deviceNo,
    unsigned long mode,               // PDC_RECMODE_* constants
    unsigned long *pErrorCode
);
```

### Live Image Acquisition

```c
// Get live image data - KEY FUNCTION FOR STREAMING
unsigned long PDC_GetLiveImageData(
    unsigned long deviceNo,
    unsigned long childNo,
    unsigned long bitDepth,           // 8 for 8-bit, 12 for 12-bit
    void *pData,                      // Buffer to receive image data
    unsigned long *pErrorCode
);

// Get live image address (alternative method)
unsigned long PDC_GetLiveImageAddress(
    unsigned long deviceNo,
    unsigned long childNo,
    unsigned long *pAddress,
    unsigned long *pErrorCode
);
```

### Recording Control

```c
// Start endless recording (loop until trigger)
unsigned long PDC_SetEndless(
    unsigned long deviceNo,
    unsigned long *pErrorCode
);

// Trigger recording
unsigned long PDC_TriggerIn(
    unsigned long deviceNo,
    unsigned long *pErrorCode
);

// Get recording status
unsigned long PDC_GetStatus(
    unsigned long deviceNo,
    unsigned long *pStatus,           // PDC_STATUS_* constants
    unsigned long *pErrorCode
);

// Get memory image data after recording
unsigned long PDC_GetMemImageData(
    unsigned long deviceNo,
    unsigned long childNo,
    long frameNo,                     // Frame number to retrieve
    unsigned long bitDepth,
    void *pData,
    unsigned long *pErrorCode
);
```

### Memory Management

```c
// Get memory partition info
unsigned long PDC_GetMemPartitionInfo(
    unsigned long deviceNo,
    unsigned long *pNum,              // Number of partitions
    unsigned long *pBlock,            // Blocks per partition
    unsigned long *pErrorCode
);

// Set active partition
unsigned long PDC_SetMemPartition(
    unsigned long deviceNo,
    unsigned long partitionNo,
    unsigned long *pErrorCode
);

// Get frame range in memory
unsigned long PDC_GetMemFrameInfo(
    unsigned long deviceNo,
    unsigned long childNo,
    long *pStart,                     // Start frame number
    long *pEnd,                       // End frame number
    long *pTrigger,                   // Trigger frame number
    unsigned long *pErrorCode
);
```

## Constants and Enumerations

### Interface Types
```c
#define PDC_INTTYPE_G_ETHER     0x00000001  // Gigabit Ethernet
#define PDC_INTTYPE_10G_ETHER   0x00000002  // 10 Gigabit Ethernet
```

### Status Codes
```c
#define PDC_STATUS_LIVE         0x00000000  // Live mode
#define PDC_STATUS_ENDLESS      0x00000002  // Endless recording
#define PDC_STATUS_REC          0x00000004  // Recording
#define PDC_STATUS_SAVE         0x00000008  // Saving
```

### Trigger Modes
```c
#define PDC_TRIGGER_START       0x00000001  // Start trigger
#define PDC_TRIGGER_CENTER      0x00000002  // Center trigger
#define PDC_TRIGGER_END         0x00000003  // End trigger
#define PDC_TRIGGER_RANDOM      0x00000004  // Random trigger
#define PDC_TRIGGER_MANUAL      0x00000005  // Manual trigger
```

### Recording Modes
```c
#define PDC_RECMODE_NORMAL      0x00000000  // Normal recording
#define PDC_RECMODE_BURST       0x00000001  // Burst recording
```

### Error Codes
```c
#define PDC_SUCCEEDED           0x00000000  // Success
#define PDC_FAILED              0x00000001  // General failure
#define PDC_ERROR_NOT_INIT      0x00000002  // SDK not initialized
#define PDC_ERROR_NOT_OPEN      0x00000003  // Device not opened
#define PDC_ERROR_TIMEOUT       0x00000004  // Communication timeout
```

## Network Configuration

### Gigabit Ethernet Setup

1. **Direct Connection Required**: Connect camera directly to 1GbE or 10GbE NIC (not through 100BASE-TX switch)
2. **Jumbo Frames**: Enable jumbo frames (9000 bytes) on NIC for optimal performance
3. **IP Configuration**: Camera defaults to 192.168.0.10; configure PC NIC to same subnet
4. **Firewall**: Add exception for application using PDCLIB

### UDP Communication
- Camera listens on UDP port 2000
- Commands sent as structured packets
- Responses returned on same port

## Data Formats

### 8-bit Image Data
- 1 byte per pixel
- Grayscale: 0-255

### 12-bit Image Data
- 1.5 bytes per pixel (packed)
- Two pixels stored in 3 bytes
- Unpack formula: `pixel1 = (byte0 << 4) | (byte1 >> 4); pixel2 = ((byte1 & 0x0F) << 8) | byte2;`

### MRAW Format
- Photron's native recording format
- Header + raw frame data
- Use pyMRAW library for Python parsing

## Third-Party Wrappers

### PyHSCam (Windows Python)
Repository: github.com/ion201/PyHSCam

```python
import PyHSCam

# Initialize and detect cameras
PyHSCam.init()
cameras = PyHSCam.detect()

# Open camera
cam = PyHSCam.open(cameras[0])

# Get live frame
frame = cam.get_live_image()

# Set parameters
cam.set_rate(1000)  # 1000 fps
cam.set_resolution(1920, 1080)

# Start recording
cam.start_endless()
cam.trigger()

# Get recorded frame
recorded_frame = cam.get_mem_image(frame_no=0)

# Cleanup
cam.close()
PyHSCam.close()
```

### photron-linux-sdk (Experimental Linux)
Repository: github.com/felix-engelmann/photron-linux-sdk

FastAPI server exposes live frames:
```python
# Camera communicates via UDP on port 2000
# FastAPI exposes frames on HTTP port 5000

import requests
response = requests.get("http://localhost:5000/live")
frame_data = response.content
```

### ADPhotron (EPICS areaDetector)
Repository: github.com/kmpeters/ADPhotron

For EPICS control systems integration.

## MATLAB/LabVIEW Integration

Official Photron drivers v4.4.0.0:
- MATLAB: Uses PDCLIB via MEX interface
- LabVIEW: VI library wrapping PDCLIB functions
- Download from Photron support or with PFV installation

## Streaming Workflow Example

```c
// 1. Initialize SDK
PDC_Init(&errorCode);

// 2. Detect and open camera
PDC_DetectDevice(PDC_INTTYPE_G_ETHER, &numCameras, 10, detectInfo, &errorCode);
PDC_OpenDevice(&detectInfo[0], &deviceNo, &errorCode);

// 3. Configure camera
PDC_SetRecordRate(deviceNo, 1, 1000, &errorCode);  // 1000 fps
PDC_SetResolution(deviceNo, 1, 1920, 1080, &errorCode);
PDC_SetShutterSpeed(deviceNo, 1, 500000, &errorCode);  // 500μs

// 4. Allocate buffer
unsigned char *buffer = malloc(1920 * 1080);  // 8-bit

// 5. Stream live frames
while (streaming) {
    PDC_GetLiveImageData(deviceNo, 1, 8, buffer, &errorCode);
    process_frame(buffer);
}

// 6. Cleanup
free(buffer);
PDC_CloseDevice(deviceNo, &errorCode);
PDC_Close(&errorCode);
```

## Performance Optimization

### Maximizing Throughput
1. Use 10GbE connection when available
2. Enable jumbo frames on both ends
3. Use dedicated NIC (not shared with other traffic)
4. Disable power saving on NIC

### Minimizing Latency
1. Use PDC_GetLiveImageData() for lowest latency
2. Process frames in separate thread
3. Use memory-mapped buffers when possible
4. Minimize frame copy operations

### Memory Management
1. Pre-allocate frame buffers
2. Use ring buffer for continuous streaming
3. Stream to RAM disk or NVMe for fastest writes
4. Consider segment recording for extended captures

## Obtaining the SDK

1. Purchase Photron camera
2. Download PFV (FASTCAM Viewer) from photron.com/pfv/
3. SDK included in PFV installation
4. Contact Photron (image@photron.com or 800-585-2129) for standalone SDK
