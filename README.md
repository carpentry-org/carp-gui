# carp-gui

A native Carp library for immediate-mode GUI rendering on WebGPU.

`carp-gui` provides an ergonomic, purely functional immediate-mode GUI API with 2D orthographic GPU rendering. It is designed to run efficiently on WebGPU pipelines, generating vertex batches dynamically on every frame.

## Features
- **SDF-Based Rounded Rectangles**: Renders panels, buttons, and sliders with clean, anti-aliased rounded corners procedurally in the fragment shader.
- **Immediate-Mode API**: Define windows, layouts, and widgets inline with your tick/render loop.
- **Automatic Vertical Layout Layout**: Automatically positions labels, buttons, and sliders within panels.
- **WebGPU Batch Geometry Generator**: Batches all panel quads and widget elements into a single vertex array `(Array Vertex)` in a single pass.
- **Zero Linking Dependencies**: Written 100% in native Carp and WGSL.

## Directory Structure
- [gui.carp](gui.carp): Core library logic including state tracking, widget functions, and geometry generator.
- [gui.wgsl](gui.wgsl): 2D WebGPU shader containing orthographic mapping, anti-aliased rounded box distance estimation, and SDF text sampling.
- [examples.md](examples.md): Practical code examples demonstrating widgets and editor viewport setups.
- [LICENSE](LICENSE): MIT License.

## Installation

```
(load "git@github.com:carpentry-org/carp-gui@master")
```

> `tests/guitest.carp` is a manual WebGPU demo, not a test. It needs
> `WGPU.carp`, `WGPURender.carp` and `carp-wgpu-scaffold`, none of which are
> in this org, so it does not run in CI. `tests/gui_test.carp` covers the
> library itself.

## Getting Started

### 1. Initialize Context
Keep a single `Context` instance representing the UI state:
```carp
(let [gui-ctx (Gui.Context.create)]
  ...)
```

### 2. Immediate-Mode Render Loop
Call UI widgets inside your update/draw tick:
```carp
(Gui.begin &gui-ctx mouse-x mouse-y mouse-down mouse-clicked)

(Gui.begin-panel &gui-ctx @"panel1" 10.0f 10.0f 250.0f 300.0f "Voxel Control Panel")
  (Gui.label &gui-ctx "Brushes")
  (if (Gui.button &gui-ctx &@"btn_reset" "Reset Mesh")
    (do-reset)
    ())
(Gui.end-panel &gui-ctx)
```

### 3. Generate GPU Vertices
At the end of the GUI definition, convert the commands to vertices and load them to WGPU:
```carp
(let [vertices (Gui.generate-vertices &gui-ctx)]
  ;; Upload vertices to WGPUBuffer and call draw
  ...)
```
