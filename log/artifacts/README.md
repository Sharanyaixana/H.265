# Reproducible HEVC Bitstream Experiment

## Purpose

Generate a small fixed test-pattern encode, inspect its stream and frame metadata, and trace selected HEVC headers. This experiment supports the bitstream presentation; it is not evidence about natural-video coding efficiency.

## Current execution status

**Not yet run.** On September 15, 2026, `ffmpeg` and `ffprobe` were not found on this machine. Install an FFmpeg build that includes `libx265` and the `trace_headers` bitstream filter before claiming experimental results [[S19]](../../SOURCES.md#s19) [[S63]](../../SOURCES.md#s63).

## 1. Record the toolchain

Run these commands from the repository root in PowerShell:

```powershell
ffmpeg -version | Select-Object -First 1
ffprobe -version | Select-Object -First 1
ffmpeg -hide_banner -encoders | Select-String "libx265"
ffmpeg -hide_banner -bsfs | Select-String "trace_headers"
```

Copy the version lines and capability results into `log/artifacts/observations.md`. If either capability is absent, stop and record that limitation.

## 2. Generate the exact input and HEVC elementary stream

The input is FFmpeg's generated `testsrc2` pattern: 640×360, 30 frames/s, two seconds. No downloaded media is required.

```powershell
ffmpeg -hide_banner -f lavfi -i "testsrc2=size=640x360:rate=30" -t 2 -an -c:v libx265 -preset medium -x265-params "keyint=30:min-keyint=30:scenecut=0" -pix_fmt yuv420p -f hevc "log/artifacts/bitstream-sample.hevc"
```

Record the complete command, FFmpeg version, x265 version printed during encoding, and whether the command succeeded. The named preset and GOP controls are encoder choices, not requirements of H.265 [[S21]](../../SOURCES.md#s21).

## 3. Inspect stream and frame information

```powershell
ffprobe -v error -select_streams v:0 -show_entries "stream=codec_name,profile,width,height,pix_fmt,level" -show_entries "frame=key_frame,pict_type,pkt_pos,pkt_size" -of json "log/artifacts/bitstream-sample.hevc" | Set-Content "log/artifacts/ffprobe-report.json"
```

Verify and record:

- codec name;
- profile and level reported by the parser;
- width, height, and pixel format;
- observed I, P, or B picture types;
- difference between byte position/size and display-picture meaning.

Do not infer NAL-unit syntax from `pict_type` alone.

## 4. Trace HEVC headers

```powershell
ffmpeg -hide_banner -i "log/artifacts/bitstream-sample.hevc" -map 0:v:0 -c copy -bsf:v trace_headers -f null NUL 2> "log/artifacts/trace-headers.txt"
```

Then locate useful fields:

```powershell
Select-String -Path "log/artifacts/trace-headers.txt" -Pattern "nal_unit_type|video_parameter_set_id|seq_parameter_set_id|slice_pic_parameter_set_id"
```

Expected categories include parameter-set NAL units and coded-slice NAL units, but record what the named tool version actually reports rather than copying this expectation as a result.

## 5. Evidence record

Create `log/artifacts/observations.md` containing:

```text
Date:
Operating system:
FFmpeg version:
FFprobe version:
x265 version:
Exact generation command:
Exact inspection commands:
Generated input description:
Observed stream fields:
Observed frame types:
Observed NAL-unit types:
What this verifies:
What this does not verify:
Unexpected output or errors:
```

The experiment becomes evidence only after these outputs exist and another person can repeat the commands. Generated media and reports should not be described as results before execution.
