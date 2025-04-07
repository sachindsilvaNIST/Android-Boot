# Android Boot Animation with NIDEC Spinner

## Overview

This project extends Android's native boot animation system by adding a **custom NIDEC-themed logo** and a **loading spinner animation** during system boot-up. The modifications are implemented in `BootAnimation.cpp`, with a focus on the `BootAnimation::nidec()` function.

The spinner plays smoothly under the **"NIDEC - All For Dreams"** logo and is optimized to render 60 frames at a steady ~20 FPS (50ms per frame).

---

## Features

- ✅ Displays NIDEC logo during boot.
- 🔄 Renders a 60-frame loading spinner animation using OpenGL ES.
- 🕒 Frame-timed rendering with `systemTime()` for smooth transitions.
- 🎯 Accurate frame rate regulation (~20fps) using `usleep`.
- 💥 Cleans up OpenGL textures on exit.
- 📦 Uses assets like `nidec-logo_landscape2.png` and `spinner_#.png`.

---

## File Structure

```plaintext
/bootanimation
├── images/
│   ├── nidec-logo_landscape2.png
│   └── spinner_1.png to spinner_60.png
├── BootAnimation.cpp
├── BootAnimation.h
└── Android.mk or Android.bp
```

---

## Code Highlights

### 1. Logo & Spinner Initialization

```cpp
initTexture(&mAndroid[0], mAssets, "images/nidec-logo_landscape2.png");
for (int i = 1; i <= 60; i++) {
    char filePath[128];
    sprintf(filePath, "images/spinner_%d.png", i);
    initTexture(&spinnerFrames[i-1], mAssets, filePath);
}
glBindTexture(GL_TEXTURE_2D, mAndroid[0].name);
glDrawTexiOES(0, 0, 0, mWidth, mHeight);
glBindTexture(GL_TEXTURE_2D, spinnerFrames[currentFrame].name);
glDrawTexiOES(spinnerX, spinnerY, 0, spinnerWidth, spinnerHeight);
currentFrame = (currentFrame + 1) % 60;
nsecs_t sleepNs = FRAME_DURATION_NS - frameElapsed;
if (sleepNs > 0) {
    usleep(ns2us(sleepNs));
}
```


## How to Integrate

1. **Replace** the default `bootanimation.zip` with a custom one containing the `nidec-logo` and `spinner` images.
2. **Update** the Android system service that launches the boot animation to call the `BootAnimation::nidec()` function.
3. **Recompile** and flash your modified system image.

---

## Notes

- Ensure all 60 spinner PNG files exist in the correct path inside the boot animation ZIP.
- Tested for landscape resolution, adjust `spinnerX` and `spinnerY` for different screen sizes.
- Compatible with OpenGL ES 1.0 (via legacy APIs).

---

## Author

**Sachin R. D'Silva**  
_NIDEC Custom Firmware Integration Engineer_  
📅 January–February 2025

