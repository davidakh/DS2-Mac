# Death Stranding 2: On The Beach — macOS Fix

Run Death Stranding 2 on Apple Silicon Macs via CrossOver/D3DMetal.

## The Problem

DS2 crashes on launch with `"Error initializing rendering configuration"` when running through CrossOver with D3DMetal. The game's Decima engine checks for two D3D12 features during initialization that Apple's D3DMetal translation layer doesn't report as supported:

1. **`DXGI_FEATURE_PRESENT_ALLOW_TEARING`** — D3DMetal returns `0` (macOS handles vsync natively)
2. **`D3D12_FEATURE_DATA_D3D12_OPTIONS2.DepthBoundsTestSupported`** — D3DMetal returns `FALSE`
3. **`IDXGIOutput6::GetDesc1().ColorSpace`** - Returning SDR on compatible HDR displays

Unlike Horizon Zero Dawn Remastered (same engine, works fine on D3DMetal), DS2 treats both as hard requirements with no fallback path — if either is missing, rendering init is aborted entirely.

Neither feature is actually needed for the game to render. Tearing is only for variable refresh rate modes, and depth bounds test is a minor deferred lighting optimization.

## Usage

```bash
# Apply the patch
python3 ds2.py "/path/to/DS2.exe"

# Restore original
python3 ds2.py --restore "/path/to/DS2.exe"
```

The typical path inside a CrossOver bottle is:
```
~/Library/Application Support/CrossOver/Bottles/<bottle>/drive_c/
Program Files (x86)/Steam/steamapps/common/
DEATH STRANDING 2 - ON THE BEACH/DS2.exe
```

A backup (`DS2.exe.backup`) is created automatically before patching.

## Requirements

- **macOS Tahoe 26.4** on Apple Silicon (M1/M2/M3/M4)
- **CrossOver Preview 20260323** with D3DMetal enabled
- **DS2 v1.0.49.0** (Steam) — other versions may need updated patterns
- **CrossOver Settings** D3DMetal, MSync, `D3DM_SUPPORT_DXR=1`

## Disclaimer

This patch modifies a copyrighted executable for personal compatibility purposes. No game code or assets are distributed. You must own a legitimate copy of Death Stranding 2. Use at your own risk.

## License

MIT
