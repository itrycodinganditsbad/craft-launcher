---
title: The launcher does not open
description: What to try when ArtCraft Launcher does nothing, closes straight away or exits before its window appears, mostly on Windows.
order: 10
verified: 0.3.2
---

On Windows, try starting the launcher with OpenGL: run `$env:WGPU_BACKEND = 'gl'` in PowerShell, then start `artcraft-launcher.exe` from the same window. The steps are below.

## Symptoms

- You click the launcher and nothing happens. It does not appear in Task Manager, even for a moment.
- The window flashes and closes.
- Reinstalling, repairing and updating graphics drivers does not help.
- The portable version may start when the installed one does not, or the other way round.

If you start the launcher from a terminal, the error ends with `Parent device is lost`.

On a PC with Intel HD or UHD Graphics 500/600 series, the launcher can exit with code 101 and the error `Shader translation error ... (0x80070057)` ([issue #111](https://github.com/storytold/craft-launcher/issues/111)).

## Fix

These causes were reported in the issue tracker. Try them in order. Windows first.

### Windows: start with OpenGL

A launcher user confirmed this works when the error mentions `dxil.dll` or `DXC compile error`. The reporter of [issue #111](https://github.com/storytold/craft-launcher/issues/111) found it works on Intel HD/UHD 500/600 graphics with the `Shader translation error ... (0x80070057)` error.

1. Open PowerShell.
2. Go to the folder with `artcraft-launcher.exe`. The installed copy is in `%LOCALAPPDATA%\Programs\ArtCraft Launcher`.
3. Run these two commands:

        $env:WGPU_BACKEND = 'gl'
        .\artcraft-launcher.exe

The launcher window should open. The setting only applies to that PowerShell window, so repeat it each time you start the launcher this way.

### Windows: add two DLL files

Two users fixed the same problem a different way. They copied `dxil.dll` and `dxcompiler.dll` into the launcher's folder.

1. Download the latest zip from the [DirectX Shader Compiler releases](https://github.com/microsoft/DirectXShaderCompiler/releases).
2. Open the `bin` folder in the zip, then the folder for your processor, usually `x64`.
3. Copy `dxil.dll` and `dxcompiler.dll` into the folder with `artcraft-launcher.exe`.
4. Start the launcher.

Both files have to come from the same version. One user found that a single `dxil.dll` was not enough.

### macOS on a Mac patched with OpenCore Legacy Patcher

The launcher quits right after its window flashes, and the terminal says `invalid value 'metal3.2'`. Users reported that this command keeps it running on some Macs:

    SYSTEM_VERSION_COMPAT=1 /Applications/ArtCraft\ Launcher.app/Contents/MacOS/*

It did not help on one Mac with Intel Iris Pro graphics.

### Intel graphics on Windows

One user reported that updating the Intel Graphics driver fixed a crash with an error naming `igvk64.dll` in the Windows Event Viewer (Windows Logs, Application).

## Why this happens

On Windows the launcher draws with Direct3D 12 and compiles its shaders with a compiler file called `dxcompiler.dll`. The launcher does not include one. If Windows finds an old or unmatched copy, the shaders fail and the window never opens. OpenGL does not use that compiler.

Status in version 0.3.2: no fix for this is released. Two pull requests that make the launcher use the compiler built into Windows are still open. When one is released, this page will say so.

## Still stuck?

Ask in [Discord](https://discord.gg/artcraft) or open an issue in the [issue tracker](https://github.com/storytold/craft-launcher/issues). Include your launcher version, your system, your graphics card, and the error text if you can get it.
