# ALC897 Layout 100 - SPDIF Fix

## Problem Identified
Based on your codec dump analysis, the existing ALC897 layouts (11, 12, etc.) were using incorrect node mappings for SPDIF output:
- **Incorrect**: Node 17 → Node 16 (used in layouts 11, 12)
- **Correct**: Node 30 (0x1e) → Node 6 (0x06) (from your hardware codec dump)

## Solution Created
I've created a new **Layout 100** with the correct SPDIF node mapping based on your actual hardware configuration.

## Files Created/Modified:

### 1. `Resources/ALC897/layout100.xml`
- New layout file identical to layout 11 but with ID 100
- Maintains all existing functionality (Headphone, IntSpeaker, LineOut, SPDIFOut)
- Uses PathMapID 1 for compatibility

### 2. `Resources/ALC897/Platforms100.xml`
- **Key Fix**: Changed SPDIF path from Node 17→16 to Node 30→6
- This matches your codec dump showing:
  - Node 0x1e (30): SPDIF Pin Complex
  - Node 0x06 (6): SPDIF Digital Audio Output

### 3. `Resources/ALC897/Info.plist`
- Added Layout 100 registration
- Comment: "Custom ALC897 with Fixed SPDIF - Layout 100"

## Hardware Verification from Your Codec Dump:
```
Node 0x06 [Audio Output] wcaps 0x611: Stereo Digital
  Device: name="ALC897 Digital", type="SPDIF", device=1
  
Node 0x1e [Pin Complex] wcaps 0x400781: Stereo Digital
  Pin Default 0x01451130: [Jack] SPDIF Out at Ext Rear
  Connection: 1
     0x06
```

## How to Test:

1. **Compile AppleALC** with the new layout files
2. **Set layout-id to 100** in your bootloader config
3. **Reboot** and test SPDIF output
4. **Verify** that "ALC897 Digital" device now produces audio

## Expected Result:
- SPDIF Out should now appear in audio devices
- Audio should properly route to your optical/coaxial SPDIF output
- Node 0x06 should show active stream assignment in codec dumps

## Technical Details:
- **SPDIF Path**: Pin 0x1e → Converter 0x06
- **Audio Routing**: System audio → Node 6 → Node 30 → Physical SPDIF port
- **Device Name**: "ALC897 Digital" (device=1)

This fix addresses the core issue where AppleALC layouts were using non-existent node paths for SPDIF on your specific hardware.
