HamTech RTL Comander V8 - RTL-SDR Blog V4
======================================

V8 AIRBAND
New left-side AIRBAND button: 118–137 MHz, AM. Clicking it tunes to
118.000000 MHz and selects AM. Use the wheel, STEP/arrows or frequency
entry to tune the required frequency within this range.

V7 CONTROLS
STEP cycles 100 Hz, 10 Hz, 1 kHz, 5 kHz and 12.5 kHz; the dropdown also works.
Hover the main frequency and scroll: up increases, down decreases by STEP.
Click the main frequency to select all, type MHz (7.100000) or grouped Hz
(7.100.000), then press Enter. Escape cancels an edit. Arrow keys also tune.
WF GAIN changes waterfall brightness from -30 to +30 dB; saved locally. It
does not change RF or audio gain. Gain changes clear previous waterfall rows.
DX CLUSTER fetches HamQTH spots via the local Python server, refreshes every
30 seconds while open, and offers a band filter and click-to-tune frequencies.
Current reception mode is retained: choose CW/LSB/USB as appropriate. Digital
spots require a separate decoder. No account or spot posting is involved.
The date/time of each report is visible; upstream feeds may be delayed.
If the feed is unavailable, the dialog reports this and retains previous rows.

V6 CAPTURE CHANGE
The video showed regular sound/silence periods. V5 still serialized synchronous
USB reads with DSP processing, which can deliver less PCM than playback needs.
V6 uses rtlsdr_read_async with 16 pending USB transfers, copying sample blocks
into a queue. A separate thread runs DSP. USB acquisition stays active while
DSP works. Tuning does not reset active asynchronous USB transfers.
SYSTEM STATUS reports PCM production (target about 48000 samples/second), USB
queue depth and USB drops. After 10 seconds, report these values with audio gaps
and skips if chuffing remains. A persistently low PCM rate means starvation;
an increasing USB queue/drop count points to processing falling behind.
A continuously growing gap count with normal PCM rate points to playback or
network/scheduling delays. These are diagnostic clues, not hardware proof.
Capture timing, transport/DSP and audio worklet software tests passed. In the
capture test, 20ms DSP delays did not delay the continuous USB callback stream.
Your physical dongle and Windows audio still require a live test.

YOUR DLLS ARE INCLUDED
rtlsdr.dll, msvcr100.dll and pthreadVC2.dll are copied byte-for-byte from your
uploaded working files. You do not need to add them again or run the installer.
The rtlsdr.dll file is 64-bit and contains Blog V4 support. It also imports the
Microsoft VCRUNTIME140 runtime. Your working laptop probably already has it;
if DLL loading fails, check the Microsoft Visual C++ x64 runtime installation.
The uploaded msvcr100.dll is a different runtime and is preserved as supplied.
Python 3.10+ 64-bit, the existing WinUSB driver, and Python dependencies are still
required. This ZIP does not include a Python interpreter or OS driver installer.

START
1. Stop the previous Python receiver window and close any SDR software using USB.
2. Extract this whole ZIP into a NEW folder. Do not launch inside the ZIP.
3. Run START-HAMTEC-DIRECT-USB-AUDIO.bat. First run installs Python dependencies
   into a local .venv folder; it requires internet access for that step.
4. Browser: http://127.0.0.1:8765 . Use Edge or Chrome. Press POWER for audio.
   If you see an older page, use Ctrl+F5. Keep the Python window open.

WFM
There is a WFM button in the bottom MODE row as well as a WFM dropdown selection.
Use WFM for broadcast FM, select 180 kHz bandwidth, and tune a known strong local
FM station. FM/NFM is narrow FM for voice communications; it is not broadcast FM.
WFM is mono with 50 microsecond de-emphasis for UK/Europe, without stereo/RDS.

CHUFFING AUDIO
Playback now runs in a dedicated AudioWorklet with a 180ms startup/jitter buffer,
resampling for the actual audio device clock, and gentle clock-rate correction.
It no longer starts/stops a separate AudioBufferSource for each USB chunk.
Audio gain changes are smoothed across samples to reduce block-edge pumping,
and limiting is soft instead of abrupt clipping. This targets buffer gaps and
block-edge discontinuities; interference or overload can still cause noise.
SYSTEM STATUS shows buffer size, gaps, skips and audio RMS. TEST SPEAKERS checks
Windows/browser output separately from reception. Record those counters if the
problem remains; distinguish an actual RF signal from an audio queue problem.

HF / V4
The driver automatically handles the V4's HF upconversion. This console uses
normal tuner mode and does NOT enable V3 Q-branch direct sampling or add a
manual 28.8MHz conversion offset.
The startup terminal and SYSTEM STATUS now report USB manufacturer/product,
V4 model recognition, tuner type and the loaded DLL path. Both frequency and
sample rate are read back from the driver. Expected sample rate: 1.024 MS/s.
A 128kHz LO offset moves the selected channel away from the RTL's centre DC spur;
DSP cancels that offset so you enter the actual desired reception frequency.
At the top tuning limit the offset is reversed. This is unrelated to the V4's
internal HF upconverter. Wrapped FFT bins at the full spectrum edge are blanked.
40m starts at 7.100000 MHz / LSB; 20m at 14.200000 MHz / USB. These are presets,
not guaranteed active stations. Use a suitable HF antenna and a known active
signal when testing. Squelch starts OFF (-100 dBFS). Increase RF gain if needed.
V4 recognition NO means the driver did not identify the expected EEPROM model;
HF support cannot then be assumed. Check the official software/model details;
this program never writes EEPROM or changes your dongle identity.

DRIVER DIAGNOSTICS
A V4-capable DLL is necessary but cannot prove the connected dongle was detected
correctly. No simulated 'HF OK' status is shown. Driver errors are displayed and
reported in the terminal. If there is no live spectrum, inspect the terminal.
Do not change a working WinUSB setup merely to use this ZIP.

Validation: four Python tests passed for USB sample streaming, squelch/sideband
filtering, digital-offset reception and WFM demodulation. Worklet tests passed
at 48kHz and 44.1kHz, including a 120ms delivery stall, continuous tone playback
and mute. Physical USB HF reception and Windows audio remain laptop tests.

Sources:
https://www.rtl-sdr.com/v4/
https://github.com/rtlsdrblog/rtl-sdr-blog
https://developer.mozilla.org/en-US/docs/Web/API/AudioWorkletProcessor

DX source: https://www.hamqth.com/dxc_csv.php
