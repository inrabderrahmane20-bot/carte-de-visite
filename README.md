# Business Card Viewer - Interactive 3D Experience

A modern, scalable business card viewer with advanced interactions built for both desktop and mobile.

## 🚀 Quick Start

1. **Open in Browser**: Double-click `business-card-viewer-v2.html`
2. **Interact**:
   - **Desktop**: Drag to rotate • Scroll to zoom • Move mouse for lighting effects
   - **Mobile**: Swipe to rotate • Pinch to zoom • Tilt device (optional)

## ✨ Features

| Feature | Desktop | Mobile | Status |
|---------|---------|--------|--------|
| **3D Rotation** | Drag | Swipe | ✅ |
| **Zoom** | Scroll Wheel | Pinch | ✅ |
| **Hover Effects** | Mouse Light | N/A | ✅ |
| **Keyboard Control** | Arrow Keys | N/A | ✅ |
| **Touch Gestures** | N/A | Swipe/Pinch | ✅ |
| **Accelerometer** | N/A | Device Tilt | ⚙️ Optional |
| **Background Effects** | Matrix • Keywords • Circuits | Optimized | ✅ |
| **Real-time HUD** | Stats & Telemetry | Scaled UI | ✅ |
| **Cloud Images** | Google Drive | Google Drive | ✅ |

## 📋 How to Use

### Desktop
```
DRAG              → Rotate card horizontally
SCROLL            → Zoom in/out
MOVE MOUSE        → Lighting effects on card
ARROW KEYS/A-D    → Keyboard rotation
SPACE/ENTER       → Gentle flick
```

### Mobile
```
SWIPE LEFT/RIGHT  → Rotate card
PINCH             → Zoom in/out
TILT DEVICE       → Optional rotation (if enabled)
TAP & HOLD        → Rotate with drag
```

## 🎮 Interactive Elements

- **Card Rotation**: Unlimited 360° rotation with physics-based momentum
- **Zoom Level**: 0.5x (far) to 3.0x (close) with smooth damping
- **Gloss Reflection**: Cursor-tracking light highlight
- **Shadow System**: Dynamic shadow that responds to rotation
- **Rotation Indicator**: Visual bar showing front/back position
- **Real-time Telemetry**: HUD displays rotation angle, velocity, zoom level, face position

## 🎯 Architecture

Built with **modular, scalable design**:

- **RotationEngine**: Handles all rotation logic with inertia
- **ZoomEngine**: Manages zoom with mouse wheel and pinch gestures
- **HoverEngine**: Cursor-based lighting effects
- **ImageLoader**: Google Drive image integration
- **Config System**: Centralized feature control

Easy to extend with new features - just add new engine modules!

## ⚙️ Customization

Edit the `CONFIG` object in the HTML to customize:

```javascript
CONFIG = {
  images: { recto: 'DRIVE_ID', verso: 'DRIVE_ID' },
  features: { rotation: true, zoom: true, hover: true, ... },
  rotation: { sensitivity: 0.52, inertiaDamping: 0.935 },
  zoom: { minScale: 0.5, maxScale: 3.0, speed: 0.15 },
  hover: { glossIntensity: 0.12, ... },
  mobile: { pinchZoomEnabled: true, tiltEnabled: false }
};
```

See `FEATURES.md` for detailed configuration options.

## 🖼️ Images

Currently uses **Google Drive links** for cloud-based image delivery:
- **Recto (Front)**: ID `1XLeMA9QGqUUM_wE-n9W5VujgHzvMb0IO`
- **Verso (Back)**: ID `180t4iLNiCrwWf8XjeMdB2FPnkzcjK03G`

To use different images:
1. Upload images to Google Drive
2. Make them "Anyone with the link can view"
3. Extract the file ID from the share link
4. Update `CONFIG.images.recto` and `CONFIG.images.verso`

**Local images** (1.png, 2.png) can be added later by modifying the image loader.

## 🎨 Visual Design

- **Dark Sci-Fi Theme**: Navy blues with cyan/green accents
- **3D Perspective**: Realistic card rotation with depth
- **Cinematic Effects**: Matrix rain, floating code, circuit traces, scanlines
- **HUD Display**: Futuristic telemetry in all corners
- **Responsive**: Adapts to all screen sizes (desktop, tablet, phone)

## 📱 Mobile Optimization

- **Touch-First Controls**: Swipe and pinch gestures
- **Performance**: Optimized animations for mobile devices
- **Responsive UI**: HUD text scales for smaller screens
- **Gesture Support**: One-finger drag, two-finger pinch
- **Battery Friendly**: Optional features can be disabled

## 🔧 Browser Support

- ✅ Chrome/Edge (latest)
- ✅ Firefox (latest)
- ✅ Safari (latest)
- ✅ Mobile browsers (iOS Safari, Chrome Mobile)
- ⚠️ IE11 (partial support)

## 📊 Performance

- **60fps**: Smooth animations powered by requestAnimationFrame
- **GPU Accelerated**: Uses CSS transforms for 3D effects
- **Optimized Rendering**: Only updates when necessary
- **No Dependencies**: Pure vanilla JavaScript, no libraries needed

## 🎓 Learning & Extending

The code is structured for easy learning and extension:

1. **Modular IIFEs**: Each system is self-contained
2. **Clear Comments**: Sections marked with clear dividers
3. **Config-Driven**: Features enabled/disabled via CONFIG
4. **Documented**: See `FEATURES.md` for technical details

To add a new feature:
```javascript
const NewEngine = (function() {
  if (!CONFIG.features.newFeature) return;
  // Your code here
  return { /* public API */ };
})();
```

## 📝 Files

```
Carte de Visite/
├── business-card-viewer-v2.html    ← USE THIS (enhanced version)
├── business-card-viewer.html       ← Original version (backup)
├── 1.png                           ← Local recto image
├── 2.png                           ← Local verso image
├── README.md                       ← This file
└── FEATURES.md                     ← Detailed feature guide
```

## 🚀 Next Steps

- **Add Music**: Integrate background music or sound effects
- **Add Animations**: More complex intro animations or particle effects
- **Add Data**: Connect to API for dynamic card content
- **Add Interactivity**: Click-to-copy contact info, share buttons
- **Add Analytics**: Track user interactions and engagement
- **Add VR**: WebXR support for immersive experiences

## 📞 Support

For issues or questions:
1. Check browser console for errors (F12)
2. Verify Google Drive image IDs are correct
3. Try disabling features in CONFIG to isolate problems
4. See `FEATURES.md` for troubleshooting section

---

**Made with ❤️ for beautiful, interactive experiences**

Version: 2.0 | Type: Scalable, Mobile-First | Architecture: Modular
