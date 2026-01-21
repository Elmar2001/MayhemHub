# macOS Firmware Update Fix

## Problem
Users reported issues with updating firmware or uploading files to PortaPack via Mayhem Hub on macOS. The symptoms included the process freezing or failing when sending files larger than ~256 bytes. This appears to be related to how WebSerial or the USB driver handles large writes on macOS.

## Solution
We have modified the `SerialProvider` to chunk large data writes into smaller 256-byte chunks. For macOS users specifically, we also added a small delay (2ms) between chunks to ensure the buffer is processed correctly without overflowing the device or driver buffers.

## Verification
If you are on macOS:
1. Connect your PortaPack.
2. Go to the File Manager or Firmware Update section.
3. Try to upload a file larger than 1KB, or perform a firmware update.
4. The process should now complete successfully without freezing.

## Technical Details
In `src/components/SerialProvider/SerialProvider.tsx`, the `write` function now checks `navigator.userAgent` for macOS.
- **Mac:** Chunks data into 256 bytes, adds 2ms delay between writes.
- **Other Platforms:** Also chunks data > 1024 bytes into 256 byte chunks (for stability) but without the artificial delay, unless it's a Mac. Small writes are still fast.
