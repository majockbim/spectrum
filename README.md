
<div align="center">

  <img width="520" alt="spectrum - Copy" src="https://github.com/user-attachments/assets/aeecb34f-0132-4a16-acf4-084cd1a19049" />
  
  **A real-time, optimized audio visualizer for the Windows console.**
  
  <!-- Badges -->
  <a href="https://github.com/majockbim/spectrum/stargazers">
    <img src="https://img.shields.io/github/stars/majockbim/spectrum?style=flat&logo=github&color=red" alt="Stars" />
  </a>
  <img src="https://img.shields.io/badge/C%2B%2B17-%2300599C.svg?style=flat&logo=c%2B%2B&logoColor=white" alt="C++17" />
  <img src="https://img.shields.io/badge/Windows-0078D6?style=flat&logo=windows&logoColor=white" alt="Windows" />
  <img src="https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat" alt="PRs Welcome" />
  <a href="https://github.com/majockbim/spectrum/blob/main/LICENSE">
    <img src="https://img.shields.io/github/license/majockbim/spectrum?style=flat&color=purple" alt="License" />
  </a>

  <br>
  <br>

  <img width="1920" alt="v1 2 0 release transparent" src="https://github.com/user-attachments/assets/8f22bc23-f894-4904-a5a0-786bb5c7447b" />


</div>

## Overview
**Spectrum** is a high-performance terminal audio visualizer built with minimalism and accuracy in mind. It captures system audio via WASAPI, processes it using the industry-standard FFTW3 library, and renders a fluid, high-contrast spectrum directly in your Windows console.

### Key Features
*   **Minimalist UI:** Focus on the music with a clean, zero-clutter interface.
*   **Professional DSP:** Featuring Hann windowing and perceptual frequency weighting for superior accuracy.
*   **Dynamic Themes:** Seamlessly switch between built-in and custom JSON themes using runtime hotkeys.
*   **Oscilloscope Mode:** Toggle a real-time Braille-based waveform view with a single keypress.
*   **Automatic Gain Control (AGC):** Rolling normalization ensures the bars always fill the screen nicely regardless of volume.
*   **High Performance:** < 5% CPU usage on modern machines with a small memory footprint of ~50MB.

## System Architecture
```mermaid
%%{init: {'theme': 'base', 'themeVariables': { 'fontFamily': 'Comic Sans MS, Comic Neue, Chalkboard SE, cursive', 'lineColor': '#000000', 'primaryTextColor': '#000000', 'edgeLabelBackground':'#ffffff'}, 'flowchart': {'curve': 'basis'}}}%%
graph LR
    classDef default fill:#ffffff,stroke:#000000,stroke-width:2px,color:#000000
    
    subgraph T1 [Thread 1: Audio Capture]
        A([WASAPI]) --> B[Accumulate<br/>Audio]
        B --> C{Buffer Full?}
        C -->|No| B
        C -->|Yes| D[Lock Mutex &<br/>Update State]
        D -->|Reset| B
    end

    subgraph Shared [Shared State]
        M[(FFT & Waveform<br/>Buffers)]
        TH[(Current Theme<br/>Settings)]
    end

    subgraph T2 [Thread 2: DSP & Render]
        E{Check Hotkeys?}
        E -->|Theme Change| TH
        E --> F[Execute FFTW3<br/>& Power Normalization]
        F --> G[Apply Windowing<br/>& Perceptual Tilt]
        G --> H[AGC / Gamma Scaling]
        H --> I([Draw ANSI Bars /<br/>Braille Waveform])
        I -->|Loop| E
    end

    %% Data flow across threads
    D == Copy Data ==> M
    M == Read Data ==> F
    TH == Load Logic ==> I
```

## The DSP Engine
At the core of the visualizer is the **Discrete Fourier Transform (DFT)**, powered by the [FFTW3 C-API](https://www.fftw.org/). 

<img width="1920" alt="dsp" src="https://github.com/user-attachments/assets/87868a4f-0c1f-4a83-9784-391a55bc4db2" />

To achieve a high level of responsiveness and accuracy, we apply:
1. **Hann Windowing:** Eliminates spectral leakage for sharp, clean frequency bins.
2. **Instant Attack / Linear Decay:** Bars rise instantly to peaks and fall with realistic, snappy physics.
3. **Perceptual Weighting:** High frequencies are boosted to match the human ear's sensitivity (Equal-loudness curves).
4. **Gamma Contrast:** Non-linear scaling crushes background noise while making main transients "pop."

## Quick Start
1. Download the latest release from the [Releases Page](https://github.com/majockbim/spectrum/releases).
2. Extract the ZIP and run `spectrum.exe`.
3. **Controls:**
    *   **1 - 9:** Switch Themes (1: Gradient, 2: Pink, 3+: Custom).
    *   **M:** Toggle Oscilloscope Mode.

## Custom Themes
Spectrum supports fully customizable JSON themes. You can change colors, visualizer modes, and even the characters used to draw the bars.
Check out the **[THEMES.md](THEMES.md)** guide for instructions on creating your own!

## Project Structure
```text
spectrum/
├── inc/                    # Header files
│   ├── audio/              # WASAPI Engine
│   ├── math/               # FFT logic
│   ├── processing/         # Audio signal processing
│   ├── settings/           # JSON/Theme management
│   └── ui/                 # Console rendering
├── src/                    # Source files
├── themes/                 # Example custom themes
├── resources/              # App icons and metadata
├── third_party/            # FFTW3 and yyjson libraries
├── THEMES.md               # Theme creation guide
└── CONTRIBUTING.md         # Build and dev instructions
```

## Contributing & Building
If you'd like to build from source or contribute to the project, please see **[CONTRIBUTING.md](CONTRIBUTING.md)**.

## Inspiration & Honorable Mentions
**[Winamp](https://en.wikipedia.org/wiki/Winamp)**: An honorable mention to the classic **Winamp** spectral visualizer, which served as  inspiration for the responsiveness, physics, and aesthetic of this project. 

**[Paint.NET](https://paint.net/)**: I used this software to create the logo for spectrum. Fun fact: I've been playing around with this software ever since I was a kid.

## References
[FFTW (org)](https://www.fftw.org/) | [yyjson (GitHub)](https://github.com/ibireme/yyjson) | [WASAPI Documentation](https://learn.microsoft.com/en-us/windows/win32/api/audioclient/)

## Contributors
Thank you to everyone who has helped build spectrum <br>
Check out [Contributors Hall of Fame](CONTRIBUTORS.md).
