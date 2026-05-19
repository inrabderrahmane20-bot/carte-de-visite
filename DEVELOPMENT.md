# Developer Guide - Business Card Viewer V2

Technical documentation for developers extending or maintaining the application.

## 🏗️ Architecture Overview

### System Design

```
┌─────────────────────────────────────────────┐
│         Background Effects (Always on)       │
│  • MatrixRain • FloatingKeywords • Circuits  │
└─────────────────────────────────────────────┘
                        ↑
┌─────────────────────────────────────────────┐
│              CONFIG (Master Settings)        │
│  • Feature Flags • Sensitivities • Limits   │
└─────────────────────────────────────────────┘
        ↑                    ↑                 ↑
    ┌───────┐          ┌──────────┐      ┌─────────┐
    │ INPUT │          │ RENDERING│      │ EFFECTS │
    └───────┘          └──────────┘      └─────────┘
       ↓                    ↓                 ↓
┌─RotationEngine    ┌─ SceneRender    ┌─HoverEngine
├─ZoomEngine        ├─CardWrapper     ├─MobileGestures
├─ImageLoader       ├─ImageDisplay    └─Accelerometer
├─MobileGestures    └─HUDDisplay
└─KeyboardControl
```

### Modular Engines

Each engine is implemented as an IIFE (Immediately Invoked Function Expression):

```javascript
const EngineFactory = (function() {
  // Private state and methods
  let state = { /* ... */ };
  
  function privateMethod() { /* ... */ }
  
  // Initialization check
  if (!CONFIG.features.myFeature) return;
  
  // Main loop
  function loop() { requestAnimationFrame(loop); }
  requestAnimationFrame(loop);
  
  // Event listeners
  document.addEventListener('event', handleEvent);
  
  // Public API
  return {
    getState: () => state,
    setState: (newState) => { state = newState; }
  };
})();
```

## 🎯 Core Engines

### 1. RotationEngine

**Purpose**: Manages 3D card rotation with physics-based inertia.

**State Variables**:
```javascript
let rot = 0;              // Current Y rotation in degrees
let velocity = 0;         // Rotation velocity (deg/frame)
let dragging = false;     // Is user currently dragging?
let lastX = 0;           // Last mouse X position
let lastDeltaX = 0;      // Last drag delta
```

**Key Methods**:
```javascript
render()    // Updates card transform and HUD
loop()      // Animation frame handler
onStart(x)  // Drag initialization
onMove(x)   // Drag update
onEnd()     // Drag release & velocity transfer
```

**Configuration**:
```javascript
CONFIG.rotation = {
  sensitivity: 0.52,      // Degrees per pixel
  inertiaDamping: 0.935,  // Friction per frame (0-1)
  minVelocity: 0.04       // Velocity threshold
}
```

**Public API**:
```javascript
RotationEngine.getRot()           // Get current rotation
RotationEngine.setRot(degrees)    // Set rotation
RotationEngine.getVelocity()      // Get velocity
RotationEngine.setVelocity(vel)   // Set velocity
```

### 2. ZoomEngine

**Purpose**: Manages zoom/scale with multiple input methods.

**State Variables**:
```javascript
let scale = 1.0;          // Current zoom scale
let zoomVelocity = 0;     // Zoom momentum
```

**Input Methods**:
- **Mouse Wheel**: `wheel` event on card wrapper
- **Touch Pinch**: Two-finger distance calculation
- **Animation**: Damped velocity-based scaling

**Configuration**:
```javascript
CONFIG.zoom = {
  minScale: 0.5,      // Minimum zoom
  maxScale: 3.0,      // Maximum zoom
  speed: 0.15,        // Scroll sensitivity
  damping: 0.92       // Inertia damping
}
```

**Public API**:
```javascript
ZoomEngine.getScale()          // Get current scale
ZoomEngine.setScale(scale)     // Set scale (with bounds)
```

**Integration**: Works with `RotationEngine` via combined transform:
```javascript
wrapper.style.transform = `rotateY(${rot}deg) scale(${scale})`;
```

### 3. HoverEngine

**Purpose**: Cursor-tracking lighting effects on desktop.

**Features**:
- Monitors mouse position
- Calculates distance from card center
- Triggers hover state when close
- Dynamically updates gloss angle and intensity
- Adds CSS class for shadow/glow effects

**Configuration**:
```javascript
CONFIG.hover = {
  enabled: true,
  glossIntensity: 0.12,
  glowIntensity: 0.3,
  liftHeight: 15      // Not currently used
}
```

**Distance Calculation**:
```javascript
const relX = (mouseX - cardRect.left) / cardRect.width;
const relY = (mouseY - cardRect.top) / cardRect.height;
const dist = Math.sqrt((relX - 0.5) ** 2 + (relY - 0.5) ** 2);
const isHovering = dist < 0.7;  // 70% distance threshold
```

**Class Toggle**:
```javascript
wrapper.classList.add('hovering');      // Adds glow effect
wrapper.classList.remove('hovering');   // Removes glow
```

### 4. ImageLoader

**Purpose**: Loads card images from Google Drive with fallbacks.

**Configuration**:
```javascript
CONFIG.images = {
  recto: '1XLeMA9QGqUUM_wE-n9W5VujgHzvMb0IO',
  verso: '180t4iLNiCrwWf8XjeMdB2FPnkzcjK03G'
}
```

**Google Drive URL Generation**:
```javascript
// Primary (faster)
`https://lh3.googleusercontent.com/d/${fileId}`

// Fallback (slower but reliable)
`https://drive.google.com/thumbnail?id=${fileId}&sz=w1200`
```

**Loading Flow**:
1. Create Image object with CORS enabled
2. Set up onload/onerror handlers
3. Attempt primary URL
4. Fall back to secondary URL on error
5. Update placeholder when loaded
6. Add 'loaded' class to fade placeholder

**Public API**:
```javascript
ImageLoader.load(imgElement, placeholderElement, googleDriveId)
// Returns: Promise<boolean>
```

### 5. MobileGestures

**Purpose**: Advanced touch interaction and device orientation.

**Gesture Types**:

**Single Touch**:
- Drag to rotate (via RotationEngine)
- Works exactly like mouse drag

**Two-Finger Pinch**:
- Calculate distance between touch points
- Compare to previous distance
- Update zoom scale accordingly
- Trigger render

**Device Tilt** (Optional):
```javascript
window.addEventListener('deviceorientation', e => {
  const gamma = e.gamma;  // Y-axis tilt
  if (Math.abs(gamma) > 10) {
    RotationEngine.setRot(RotationEngine.getRot() + gamma * 0.3);
  }
});
```

**iOS Permission Request**:
```javascript
if (typeof DeviceOrientationEvent.requestPermission === 'function') {
  DeviceOrientationEvent.requestPermission().then(permission => {
    if (permission === 'granted') {
      // Enable accelerometer
    }
  });
}
```

## 🎨 Visual System

### CSS Architecture

**Root Variables** (in `:root`):
```css
--navy: #04071a;           /* Main background */
--accent: #2a7cff;         /* Primary highlight */
--cyan: #00d4ff;           /* Secondary highlight */
--green: #00ff8c;          /* Accent color */
--card-w: min(88vw, 680px);  /* Card width */
--card-h: calc(var(--card-w) * 0.5625);  /* Aspect ratio */
```

**3D Transform Chain**:
```
card-wrapper
  ↓ (rotateY + scale)
card-face--front / --back
  ↓ (translateZ)
img + gloss + edge-strips
  ↓ (perspective maintained)
Visual output
```

### HUD Display System

**Four Corners**:
```
┌──────────────────────────────────┐
│ TL (Engineer Info)    TR (Status) │
│                                   │
│                   [CARD]          │
│                                   │
│ BL (Telemetry)      BR (Settings) │
└──────────────────────────────────┘
```

**HUD Elements Updated**:
- `#hud-rot-disp`: Rotation angle
- `#hud-face`: Current visible face
- `#hud-vel`: Rotation velocity
- `#hud-zoom`: Zoom level (NEW)
- `#hud-time`: Current time (updated every 1s)

### Transform Pipeline

```javascript
// Final transform combining all systems
wrapper.style.transform = 
  `rotateY(${rot}deg) scale(${scale})`;

// Additional effects via CSS classes
wrapper.classList.add('hovering');      // Adds glow
wrapper.classList.add('dragging');      // Changes cursor
```

## 🔧 Event System

### Mouse Events
```javascript
// Rotation
mousedown  → onStart(x)
mousemove  → onMove(x)
mouseup    → onEnd()
mouseleave → onEnd()

// Zoom
wheel      → Update zoomVelocity
```

### Touch Events
```javascript
// Rotation
touchstart  → onStart(touches[0].x)
touchmove   → onMove(touches[0].x)
touchend    → onEnd()
touchcancel → onEnd()

// Zoom (2 fingers)
touchmove (2 fingers) → Calculate distance → Update zoom
```

### Keyboard Events
```javascript
ArrowLeft / A    → velocity -= 3
ArrowRight / D   → velocity += 3
Space / Enter    → Flip velocity (gentle flick)
```

### Window Events
```javascript
resize           → Update canvas dimensions, card rect
deviceorientation → Update rotation (if enabled)
```

## 📊 Data Flow

### Rotation Update Loop
```
Input Event (mouse/touch/keyboard)
    ↓
Update state (dragging, lastX, velocity)
    ↓
requestAnimationFrame(loop)
    ↓
Calculate new rotation (with inertia)
    ↓
render() function
    ↓
Update DOM transform
    ↓
Update HUD display
    ↓
Visual feedback
```

### Zoom Update Loop
```
wheel / pinch event
    ↓
Update zoomVelocity
    ↓
requestAnimationFrame(loop)
    ↓
Apply damping to velocity
    ↓
Update scale (within bounds)
    ↓
render() function
    ↓
Update transform + HUD
    ↓
Visual feedback
```

## 🚀 Performance Optimization

### GPU Acceleration
```css
will-change: transform;  /* Hints browser to optimize */
transform: translate3d(...);  /* Forces GPU path */
```

### Event Optimization
```javascript
// Touch events marked as passive where possible
{ passive: false }  // Only when preventDefault needed

// Mouse move not debounced (60fps is acceptable)
// Touch moves only processed when dragging
if (!dragging) return;
```

### Rendering Strategy
```javascript
// Single animation loop for all systems
requestAnimationFrame(loop);  // Synced with 60fps

// No redundant renders - render() only on change
if (!dragging && Math.abs(velocity) < MIN_VELOCITY) return;
```

## 🧪 Adding Features

### Template: New Interaction Type

```javascript
const NewInteraction = (function() {
  if (!CONFIG.features.newFeature) return;
  
  // Private variables
  let state = {};
  let isActive = false;
  
  // Initialize
  function init() {
    document.addEventListener('customEvent', handleEvent);
  }
  
  // Handle events
  function handleEvent(e) {
    state.value = e.detail;
    updateUI();
  }
  
  // Update
  function updateUI() {
    // Update DOM/HUD
  }
  
  // Animation loop (if needed)
  function loop() {
    requestAnimationFrame(loop);
  }
  
  if (CONFIG.features.newFeature) {
    init();
    requestAnimationFrame(loop);
  }
  
  // Public API
  return {
    getState: () => state,
    reset: () => { state = {}; }
  };
})();
```

### Template: New Engine

```javascript
const TemplateEngine = (function() {
  if (!CONFIG.features.template) return;
  
  const wrapper = document.getElementById('cardWrapper');
  let value = 0;
  
  function render() {
    // Update visuals
  }
  
  function loop() {
    // Animation logic
    render();
    requestAnimationFrame(loop);
  }
  
  // Event listener
  wrapper.addEventListener('click', () => {
    value = (value + 1) % 100;
  });
  
  requestAnimationFrame(loop);
  
  return {
    getValue: () => value,
    setValue: (v) => { value = v; render(); }
  };
})();
```

## 🐛 Debugging

### Console Logging
```javascript
// Add to any engine
console.log('Engine state:', { rot, velocity, dragging });
console.log('Transform:', wrapper.style.transform);
```

### Common Issues

**Images not loading**:
- Check browser console for CORS errors
- Verify Google Drive IDs are correct
- Check if files are publicly shared
- Inspect Network tab in DevTools

**Zoom not working**:
- Verify `CONFIG.features.zoom = true`
- Check if `ZoomEngine` is initialized
- Look for wheel event in DevTools

**Mobile gestures unresponsive**:
- Check touch events in DevTools
- Verify `CONFIG.features.mobileGestures = true`
- Test on actual device (not all simulators support gestures)
- Check for JavaScript errors

### Performance Profiling
```javascript
// Add to any loop
console.time('frame');
// ... your code ...
console.timeEnd('frame');

// Performance API
performance.mark('start');
// ... code ...
performance.mark('end');
performance.measure('operation', 'start', 'end');
```

## 📚 File Structure

```
business-card-viewer-v2.html
├── <head>
│   ├── Meta tags
│   ├── Fonts (Google Fonts)
│   └── <style> (All CSS)
│       ├── Reset & Root variables
│       ├── Layer stack (grid, matrix, etc.)
│       ├── HUD styling
│       ├── Scene & card styling
│       └── Media queries (mobile)
└── <body>
    ├── HTML Structure
    │   ├── Background layers (div, canvas, svg)
    │   ├── HUD corners
    │   └── Scene (card wrapper)
    └── <script> (All JavaScript)
        ├── CONFIG object
        ├── Background systems
        ├── HUD updates
        ├── Image loader
        ├── RotationEngine
        ├── ZoomEngine
        ├── HoverEngine
        └── MobileGestures
```

## 🔐 Security Notes

- **No sensitive data**: URLs are public Google Drive links
- **CORS enabled**: Images are proxied through Google servers
- **No external APIs**: All code is self-contained
- **No tracking**: No analytics by default (can be added)

## 📈 Future Extension Points

1. **Multi-Card Support**: Array of cards, pagination
2. **Card Data API**: Connect to backend for dynamic content
3. **Export Feature**: Screenshot, video, or PDF export
4. **Analytics**: Track interactions and engagement
5. **Theming**: User-selectable color schemes
6. **Sound**: Audio effects and music
7. **AR**: WebXR for immersive experiences
8. **PWA**: Offline support and app installation

## 🎓 Learning Resources

- **3D CSS Transforms**: MDN - CSS Transforms
- **Touch Events**: MDN - Touch Events
- **RequestAnimationFrame**: MDN - RAF
- **Google Drive API**: Google Developers
- **Performance**: Web.dev Performance Guide

---

**Version**: 2.0  
**Last Updated**: 2024  
**Maintainer**: Development Team
