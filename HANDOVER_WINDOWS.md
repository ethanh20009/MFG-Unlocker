# Windows handover: issue #144

## Fix and status

The issue fix is in this branch. At `slUpgradeInterface`, when the game's
factory is an MFG Unlocker menu proxy, the hook transfers its reference to the
underlying native DXGI factory before calling Streamline. It then wraps the
upgraded result for the menu. The log records `unwrappedMenuFactory=1` when the
fix applies.

This source change has not been compiled or tested in Starfield. First run
`git diff --check`, build the DLL, and perform the runtime check below.

## Windows build inputs

Follow the pinned versions and SHA-256 checks in `BUILD.md` and
`source/native/build.ps1`. The complete release build requires Visual Studio
2022 with MSVC 14.38.33130, Windows SDK 10.0.22621.0, CMake 4.3.3, Python
3.10+, Streamline SDK 2.14.1, Dear ImGui from ReShade 6.8.0, Vulkan SDKs
1.2.176.1 and 1.4.363.0, XeSS SDK 3.0.2, and CUDA Toolkit 13.2.

It also requires the separately prepared XeSS program set, xvk program set,
and validated Ampere kernel caches listed in `BUILD.md`. They are not in this
source checkout. The build script validates the pinned tool, SDK, and data
hashes; use the matching prepared inputs.

## Build command

From PowerShell in the repository root, point the script at the local inputs:

```powershell
./source/native/build.ps1 `
    -StreamlineRoot 'C:/SDKs/streamline-sdk-v2.14.1' `
    -ImGuiRoot 'C:/SDKs/reshade-6.8.0/deps/imgui' `
    -NativeCacheRoot 'C:/BuildInputs/rtxmfg-sm86' `
    -XessSdkRoot 'C:/SDKs/xess-sdk-3.0.2' `
    -XessProgramsRoot 'C:/BuildInputs/xess-programs' `
    -XvkProgramsRoot 'C:/BuildInputs/xvk-programs' `
    -BuildDirectory "$PWD/build/issue-144" `
    -EnableSessionDiagnostics
```

The script builds `RTXMFGUnified` and writes
`build/issue-144/Release/MFGUnlocker.dll`, then prints its SHA-256.

## Runtime check

Place the built DLL as `version.dll` beside `Starfield.exe`. Enable Frame
Generation, toggle it off and on, and confirm the game does not crash and the
MFG Unlocker menu still opens. Check the runtime log for
`unwrappedMenuFactory=1`; attach the log and any crash dump to issue #144 if it
still fails.
