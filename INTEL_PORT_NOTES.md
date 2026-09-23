# Vorssaint Intel Mac port

This copy keeps the existing UI source unchanged and adds Intel (`x86_64`) support in the build and hardware-service layers.

## What changed

- `build.sh` now targets the host Mac architecture automatically (`arm64` or `x86_64`).
- Intel SMC CPU temperature keys (`TCxx`) and GPU temperature keys (`TGxx`) are discovered.
- Fan telemetry continues to use the existing `FNum` / `F?Ac` keys.
- Fan control supports both the existing per-fan `F?Md` mode and the classic Intel `FS! ` force-mode bitmask, while preserving the existing RPM bounds and safety checks.
- The upstream self-updater is suppressed on Intel so it cannot replace this x86_64 build with an upstream arm64-only release.
- No files under `Sources/Vorssaint/UI` were modified.

## Requirements

- An Intel Mac capable of running macOS 14 Sonoma or newer.
- Xcode Command Line Tools (`xcode-select --install`).

## Build and test on the Intel Mac

From Terminal, inside this folder:

```sh
chmod +x build.sh
./build.sh --test
./build.sh
./build/Vorssaint --selftest
./build/Vorssaint --sensors
./build.sh --install
```

On an Intel Mac, `build.sh` automatically selects `x86_64-apple-macosx14.0`.

To force an Intel cross-build from another Mac with an appropriate macOS SDK:

```sh
VORSSAINT_ARCH=x86_64 ./build.sh
```

## Verification note

The source was syntax-checked in the provided environment, including every Swift file, and the UI directory was verified byte-for-byte unchanged from the uploaded source. A true macOS/x86_64 build and SMC fan-control test still need to run on Intel Mac hardware because this workspace is Linux and does not contain Apple's macOS SDK or AppleSMC hardware.

If `--selftest` succeeds but a temperature/fan reading is missing on your exact model, save the output of:

```sh
./build/Vorssaint --sensors
```

That output identifies the SMC keys exposed by the machine and is enough to refine a model-specific sensor mapping without changing the UI.
