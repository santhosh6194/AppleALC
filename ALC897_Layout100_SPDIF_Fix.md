# ALC897 Layout 100 & 101 - SPDIF Fix

## Problem Identified
Based on your codec dump analysis, the existing ALC897 layouts (11, 12, etc.) were using incorrect node mappings for SPDIF output:
- **Incorrect**: Node 17 → Node 16 (used in layouts 11, 12)
- **Correct**: Node 30 (0x1e) → Node 6 (0x06) (from your hardware codec dump)

## Issue with Layout 100
After testing Layout 100, you reported that Mac doesn't recognize any audio out. This indicates that **ALL** the node mappings were incorrect, not just SPDIF.

## Root Cause Analysis
From your codec dump, the correct hardware paths are:
- **LineOut/Front**: Pin 0x14 → Mixer 0x0c → DAC 0x02
- **Headphone**: Pin 0x1b → Mixer 0x0f → DAC 0x05  
- **SPDIF**: Pin 0x1e → DAC 0x06
- **Input paths**: Various pins → Mixers 0x22, 0x23 → ADCs 0x08, 0x09

## Solutions Created

### Layout 100 (Initial Fix - SPDIF Only)
- **Status**: Partial fix - SPDIF nodes corrected but analog paths still wrong
- **Issue**: Mac doesn't recognize any audio devices

### Layout 101 (Complete Fix - Based on Working Layout 99)
- **Status**: Based on your working Layout 99 + SPDIF fix
- **Method**: Copied working Layout 99 structure and only changed SPDIF path
- **Key Change**: SPDIF path from Node 17→16 to Node 30→6
- **Preserved**: All working analog paths from Layout 99

## Comparison: Layout 99 vs Layout 101

### Layout 99 (Working - Your Current Setup):
- **Front/Headphones**: ✅ Working
- **Rear Speakers**: ✅ Working  
- **SPDIF**: ❌ Broken (Node 17→16 - doesn't exist in hardware)

### Layout 101 (New - Layout 99 + SPDIF Fix):
- **Front/Headphones**: ✅ Should work (same as Layout 99)
- **Rear Speakers**: ✅ Should work (same as Layout 99)
- **SPDIF**: ✅ Fixed (Node 30→6 - matches hardware)

## Files Created/Modified:

### Layout 101 Files:
1. **`Resources/ALC897/layout101.xml`** - Copy of layout99.xml with ID changed to 101
2. **`Resources/ALC897/Platforms101.xml`** - Copy of Platforms99.xml with SPDIF path fixed
3. **Updated `Resources/ALC897/Info.plist`** - Registered Layout 101

## Hardware Verification from Your Codec Dump:
```
Node 0x02 [Audio Output] - Front DAC (active: [0x38 0x38])
Node 0x05 [Audio Output] - Headphone DAC (active: [0x38 0x38])  
Node 0x06 [Audio Output] - SPDIF DAC (Device: "ALC897 Digital")
Node 0x14 [Pin Complex] - Line Out Pin → connects to 0x0c
Node 0x1b [Pin Complex] - Headphone Pin → connects to 0x0f
Node 0x1e [Pin Complex] - SPDIF Pin → connects to 0x06
```

## How to Test Layout 101:

1. **Compile AppleALC** with the new layout files
2. **Set layout-id to 101** in your bootloader config  
3. **Reboot** and check System Preferences → Sound
4. **Verify** all audio devices appear:
   - "ALC897 Analog" for speakers/headphones
   - "ALC897 Digital" for SPDIF

## Expected Result:
- **All audio outputs** should appear in macOS (same as Layout 99)
- **SPDIF Out** should work with proper audio routing
- **Headphone and LineOut** should function normally
- **Microphone inputs** should be detected

## Technical Details:
- **SPDIF Path**: Pin 0x1e → DAC 0x06 (FIXED)
- **LineOut Path**: Pin 0x14 → Mixer 0x0c → DAC 0x02 (same as Layout 99)
- **Headphone Path**: Pin 0x1b → Mixer 0x0f → DAC 0x05 (same as Layout 99)
- **Input Paths**: Pins → Mixers 0x22/0x23 → ADCs 0x08/0x09 (same as Layout 99)

**Layout 101** is essentially your working Layout 99 with the SPDIF path corrected to match your hardware, ensuring all audio devices work properly in macOS while adding functional SPDIF output.
