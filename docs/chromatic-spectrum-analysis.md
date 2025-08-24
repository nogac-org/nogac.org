# Chromatic Spectrum Analysis

## Overview

The **Chromatic Spectrum** is the primary instrument displayed on the index page of this music theory application. It represents a sophisticated visual instrument that bridges the gap between musical frequencies and light wavelengths, demonstrating the mathematical relationships between sound and electromagnetic radiation.

## Instrument Identification

**Located**: [`content/index.md:9`](../content/index.md:9)
```markdown
<a href="/theory/interplay/spectrum/" ><img class="w-full" alt="Chromatic Spectrum" src="/img/spectrum.svg" /></a>
```

The chromatic spectrum is displayed as an interactive SVG image that links to a theoretical page about spectral interplay.

## Implementation Details

### Core Component: ColorSpectrum.vue

**File**: [`components/color/ColorSpectrum.vue`](../components/color/ColorSpectrum.vue:1)

This Vue component implements the interactive chromatic spectrum with the following key features:

#### Dependencies
- [`color-spectrum`](../package.json:86) - npm package for converting spectrum/wavelength to color
- [`pitchFreq, pitchColor`](../components/color/ColorSpectrum.vue:3) - utility functions from the application's use module
- VueUse gestures for interactivity

#### Key Functionality

1. **Frequency-to-Wavelength Conversion** ([`ColorSpectrum.vue:24-27`](../components/color/ColorSpectrum.vue:24))
   ```javascript
   function tHzToNM(frequencyTHz) {
     const speedOfLight = 299792458 * 1e9; // nm/s
     return speedOfLight / (frequencyTHz * 1e12);
   }
   ```

2. **Multi-Octave Frequency Mapping** ([`ColorSpectrum.vue:32-47`](../components/color/ColorSpectrum.vue:32))
   - Maps musical notes across octaves (-6th to 44th octave)
   - Converts audio frequencies to light frequencies (THz range)
   - Generates corresponding colors using the color-spectrum package

3. **Interactive Tuning** ([`ColorSpectrum.vue:52-66`](../components/color/ColorSpectrum.vue:52))
   - Drag gesture to adjust base frequency (A4)
   - Wheel gesture for fine-tuning
   - Double-click to reset to 440 Hz

4. **Visual Representation** ([`ColorSpectrum.vue:84-91`](../components/color/ColorSpectrum.vue:84))
   - 500-segment spectrum visualization
   - Frequency range: 400-1000 Hz
   - Real-time color calculation for each segment

## Supporting Infrastructure

### Pitch and Color Calculations

**File**: [`use/calculations.js`](../use/calculations.js:1)

Key functions supporting the chromatic spectrum:

1. **`pitchFreq()`** ([`calculations.js:25-41`](../use/calculations.js:25))
   - Converts pitch + octave to Hz frequency
   - Supports equal and just temperament tuning
   - Configurable A4 reference frequency

2. **`pitchColor()`** ([`calculations.js:46-51`](../use/calculations.js:46))
   - Generates HSL color for any pitch/octave combination
   - Formula: `hsla(${(pitch % 12) * 30}, ${velocity * 100}%, ${Math.abs(octave + 2) * 8}%, ${alpha})`

3. **`freqColor()`** ([`calculations.js:56-58`](../use/calculations.js:56))
   - Direct frequency-to-color conversion
   - Bridges audio frequencies to visual representation

### Color System Integration

**File**: [`use/colors.js`](../use/colors.js:1)

The application includes a comprehensive color system:

- **Default Scheme**: 12-tone chromatic color wheel ([`colors.js:21-24`](../use/colors.js:21))
- **Custom Colors**: User-customizable pitch colors ([`colors.js:25-35`](../use/colors.js:25))
- **Chroma Color Mixing**: Multi-note color blending ([`colors.js:98-112`](../use/colors.js:98))

## Technical Architecture

### Spectrum-to-Color Pipeline

1. **Musical Input** → Musical note + octave
2. **Frequency Calculation** → [`pitchFreq()`](../use/calculations.js:25) converts to Hz
3. **Octave Scaling** → Multiply by 2^40 to reach THz range
4. **Wavelength Conversion** → [`tHzToNM()`](../components/color/ColorSpectrum.vue:24) converts THz to nanometers  
5. **Color Generation** → [`color-spectrum`](../node_modules/.pnpm/color-spectrum@1.1.3/node_modules/color-spectrum/index.js:12) package converts wavelength to RGB

### External Dependencies

**color-spectrum Package** ([`package.json:86`](../package.json:86))
- Version: 1.1.3  
- Purpose: Convert spectrum intensities or wavelengths to colors
- Implementation: Maps visible light spectrum (380-780nm) to RGB values

## Related Components

### Chroma Components Directory
**Location**: [`components/chroma/`](../components/chroma/)

The application includes extensive chromatic visualization components:

- [`ChromaCircle.vue`](../components/chroma/ChromaCircle.vue) - Circular chromatic representation
- [`ChromaKeys.vue`](../components/chroma/ChromaKeys.vue) - Keyboard-style chromatic interface  
- [`ChromaPiano.vue`](../components/chroma/ChromaPiano.vue) - Piano keyboard with chromatic coloring
- [`ChromaFlower.vue`](../components/chroma/ChromaFlower.vue) - Floral chromatic pattern visualization
- [`ChromaSquare.vue`](../components/chroma/ChromaSquare.vue) - Grid-based chromatic display

### Audio Analysis Integration

The codebase includes sophisticated audio analysis capabilities:
- **Meyda** integration for spectral analysis
- **audiomotion-analyzer** for real-time spectrum visualization  
- **FFT** processing for frequency domain analysis

## Key Insights

1. **Instrument Type**: The Chromatic Spectrum is a **synesthetic instrument** that translates between auditory and visual domains.

2. **Mathematical Foundation**: Based on the wave nature of both sound and light, using direct frequency relationships.

3. **Interactive Design**: Provides real-time adjustment of tuning reference, making it both educational and practical.

4. **Multi-Octave Scope**: Spans from sub-audio frequencies (-6th octave) to light frequencies (44th octave), demonstrating the continuity of wave phenomena.

5. **Educational Value**: Demonstrates the relationship between musical intervals and electromagnetic spectrum colors.

## Missing Components

**Note**: The index page links to `/theory/interplay/spectrum/` but this content page does not exist in the current codebase. This suggests either:
- Planned future content about spectral theory
- A broken link that needs repair  
- Dynamic content generation not evident in the static file structure

## Conclusion

The Chromatic Spectrum represents a sophisticated fusion of music theory, physics, and interactive visualization. It serves as both an educational tool and a functional instrument for exploring the relationships between sound and light frequencies across multiple octaves of the electromagnetic spectrum.