# Business Card Viewer V2 - Enhanced Features Guide

## Overview
The new `business-card-viewer-v2.html` is a completely refactored, scalable version with support for zoom, hover effects, and advanced mobile gestures. All features are controlled through a centralized CONFIG system for easy customization.

## 🎯 Core Features

### 1. **Rotation Engine** ✓
- **Free Rotation**: Drag the card horizontally to rotate it 360°
- **Inertia Physics**: Smooth momentum-based rotation with damping
- **Unlimited Spin**: Rotate infinitely in either direction
- **Keyboard Control**: Use arrow keys (←/→) or A/D keys for rotation
- **HUD Display**: Real-time velocity and rotation angle display

**Desktop**: Click and drag horizontally  
**Mobile**: Swipe left/right to rotate  
**Keyboard**: Arrow keys or A/D keys

### 2. **Zoom Engine** ✓
- **Mouse Wheel**: Scroll up/down to zoom in/out (0.5x to 3.0x)
- **Pinch Gesture**: Two-finger pinch on touch devices to zoom
- **Smooth Animation**: Damped zoom with inertia
- **Scale Display**: HUD shows current zoom level (zoom: 1.00x)
- **Bounds Checking**: Constrained between 0.5x (zoomed out) and 3.0x (zoomed in)

**Desktop**: Scroll wheel to zoom  
**Mobile**: Pinch with two fingers to zoom

### 3. **Hover Effects** ✓
- **Cursor-Tracking Gloss**: Specular reflection follows mouse position
- **Dynamic Lighting**: Light intensity changes based on distance from card center
- **Proximity Glow**: Card glows and elevates when hovered over
- **Smart Distance**: Hover effect activates within ~70% distance from center
- **Smooth Transitions**: Smooth fade in/out of effects

Works best on **desktop** with mouse; disabled on mobile for performance.

### 4. **Mobile Optimization** ✓
- **Touch-First**: All touch events are optimized for mobile
- **Responsive Layout**: Adapts to all screen sizes
- **Gesture Support**: 
  - **One-finger drag**: Rotate the card
  - **Two-finger pinch**: Zoom in/out
  - **Accelerometer (optional)**: Tilt device to rotate (iOS/Android)
- **Performance**: Reduced animation complexity on mobile devices
- **HUD Scaling**: UI text scales appropriately for small screens

### 5. **Background Effects** ✓
All original effects are preserved:
- **Matrix Rain**: Animated code characters falling
- **Floating Keywords**: Programming keywords drifting across screen
- **Circuit Traces**: Animated circuit board paths with blinking LEDs
- **Grid Layer**: Subtle perspective grid background
- **Scanline Sweep**: CRT monitor effect overlay

### 6. **Google Drive Integration** ✓
- **Cloud-Based Images**: Loads card images from Google Drive directly
- **Automatic Fallback**: Tries multiple URLs for reliable loading
- **CORS Compatible**: Cross-origin fetching properly configured
- **Cached Loading**: Uses browser cache to avoid repeated downloads

**Current Images:**
- Recto (Front): `1XLeMA9QGqUUM_wE-n9W5VujgHzvMb0IO`
- Verso (Back): `180t4iLNiCrwWf8XjeMdB2FPnkzcjK03G`

## 🛠️ Configuration System

Edit the `CONFIG` object at the top of the JavaScript to customize behavior:

```javascript
const CONFIG = {
  images: { recto: 'DRIVE_ID', verso: 'DRIVE_ID' },
  features: { rotation: true, zoom: true, hover: true, mobileGestures: true },
  rotation: { sensitivity: 0.52, inertiaDamping: 0.935 },
  zoom: { minScale: 0.5, maxScale: 3.0, speed: 0.15, damping: 0.92 },
  hover: { glossIntensity: 0.12, glowIntensity: 0.3 },
  mobile: { pinchZoomEnabled: true, swipeRotationEnabled: true, tiltEnabled: false }
};
```

### Configuration Options

| Setting | Type | Default | Description |
|---------|------|---------|-------------|
| `images.recto` | string | ID | Google Drive file ID for front |
| `images.verso` | string | ID | Google Drive file ID for back |
| `rotation.sensitivity` | number | 0.52 | Degrees per pixel dragged |
| `rotation.inertiaDamping` | number | 0.935 | Friction (0=instant stop, 1=no friction) |
| `zoom.minScale` | number | 0.5 | Minimum zoom level |
| `zoom.maxScale` | number | 3.0 | Maximum zoom level |
| `zoom.speed` | number | 0.15 | Scroll wheel zoom speed |
| `zoom.damping` | number | 0.92 | Zoom inertia damping |
| `hover.glossIntensity` | number | 0.12 | Gloss reflection brightness |
| `mobile.tiltEnabled` | boolean | false | Enable device accelerometer |

## 📱 Mobile Features

### Gesture Support
- **Drag**: One finger swipe to rotate
- **Pinch**: Two fingers to zoom
- **Tilt** (optional): Rotate device to control rotation

### Performance Optimization
- Reduced background animation complexity on mobile
- Touch-optimized event handling (no 300ms delay)
- Adaptive HUD size for smaller screens
- Hardware acceleration for smooth 3D transforms

### Accessibility
- Full keyboard navigation (arrow keys, space, enter)
- ARIA labels for screen readers
- Tab navigation support
- Touch-action constraints for better control

## 🎨 Visual Enhancements

### Lighting & Reflections
- **Dynamic Gloss**: Follows cursor position, reflects light realistically
- **Card Glow**: Subtle glow effect on hover (desktop only)
- **Shadow System**: Shadow compresses when card is edge-on
- **Specular Highlights**: Bright highlights on card surface

### 3D Effects
- **Perspective Transform**: Card rotates in 3D space
- **Backface Culling**: Efficient rendering of card faces
- **Edge Highlights**: Physical beveled edges on card
- **Depth Shadows**: Dynamic shadow based on rotation

## 🚀 Performance Features

- **RequestAnimationFrame**: Smooth 60fps animation loop
- **Transform Optimization**: Uses GPU-accelerated transforms
- **Will-Change**: Hints to browser for optimization
- **Event Debouncing**: Prevents redundant calculations
- **Lazy Rendering**: Only renders when needed

## 🔧 Advanced Features (Optional)

### Accelerometer Support (iOS/Android)
Enable device tilt control:
```javascript
CONFIG.mobile.tiltEnabled = true;
```

On iOS 13+, users will see a prompt to grant access.

### Custom Sensitivity
Adjust rotation feel:
```javascript
CONFIG.rotation.sensitivity = 0.75; // Higher = more responsive
```

### Zoom Range
Change zoom limits:
```javascript
CONFIG.zoom.minScale = 0.3;  // Zoom out more
CONFIG.zoom.maxScale = 5.0;  // Zoom in more
```

## 📊 HUD Display

The heads-up display shows real-time stats:

**Top-Left**: Engineer info (static)  
**Top-Right**: Status & time (updates every second)  
**Bottom-Left**: Rotation angle, current face, velocity, zoom level  
**Bottom-Right**: Configuration settings (static)

## 🐛 Troubleshooting

### Images Not Loading
- Check Google Drive file IDs in CONFIG.images
- Verify files are set to "Anyone with the link can view"
- Check browser console for CORS errors

### Zoom Not Working
- Ensure `CONFIG.features.zoom = true`
- Check if zoom event listeners are attached
- Try different zoom bounds: `CONFIG.zoom.minScale` / `maxScale`

### Mobile Gestures Not Responding
- Enable `CONFIG.features.mobileGestures = true`
- For pinch zoom: `CONFIG.mobile.pinchZoomEnabled = true`
- Check browser console for JavaScript errors

### Hover Effects Missing
- Hover only works on desktop (mouse support required)
- Check `CONFIG.features.hover = true`
- Verify hover distance threshold (~70% from center)

## 📝 Usage Examples

### Disable all effects except rotation
```javascript
CONFIG.features = {
  rotation: true,
  zoom: false,
  hover: false,
  mobileGestures: false,
  keyboard: false,
  accelerometer: false
};
```

### Mobile-optimized settings
```javascript
CONFIG.rotation.sensitivity = 0.35;  // Less sensitive
CONFIG.zoom.speed = 0.08;            // Gentler zoom
CONFIG.mobile.pinchZoomEnabled = true;
CONFIG.mobile.tiltEnabled = false;   // Save battery
```

### High-precision rotation for presentations
```javascript
CONFIG.rotation.sensitivity = 0.25;  // Fine control
CONFIG.rotation.inertiaDamping = 0.98; // Smooth coasting
```

## 🎬 Next Steps

The architecture is fully modular and scalable:

1. **Add new features**: Create new engine modules (e.g., `ParticleEngine`, `SoundEngine`)
2. **Customize visuals**: Modify CSS variables in `:root`
3. **Change colors**: Update `--navy`, `--accent`, `--cyan`, etc.
4. **Add animations**: Extend background systems (Matrix, Floating Keywords, Circuits)
5. **Integrate APIs**: Connect to backends for dynamic card data

## ✨ Files

- `business-card-viewer-v2.html` - New enhanced version (this file)
- `business-card-viewer.html` - Original version (backup)
- `1.png` & `2.png` - Local image files (not currently used, images load from Google Drive)

---

**Version**: 2.0  
**Last Updated**: 2024  
**Architecture**: Modular, Config-Driven, Mobile-First
