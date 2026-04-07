# WebAssembly Code Explorer

A browser-based tool for visually exploring WebAssembly (`.wasm`) binary files. It presents a synchronized **hex dump** and **disassembly view** side by side, with color-coded sections and bidirectional click-to-highlight navigation.

![WebAssembly Code Explorer](./bug1154731b.png)

---

## Features

- 📂 **Open any `.wasm` file** directly from your local machine
- 🎨 **Color-coded hex dump** — each byte group is colored by its WebAssembly section type (functions, exports, imports, operators, etc.)
- 🔗 **Bidirectional navigation** — click a byte in the hex view to highlight the corresponding instruction, and vice versa
- 📝 **Disassembly panel** — full WAT (WebAssembly Text Format) disassembly with byte offsets
- 🗺️ **Source map support** — load a source map alongside your `.wasm` file to see the original source lines (C, Rust, etc.)
- 🏷️ **Hover annotations** — hover over any byte group to see detailed info: section type, operator name, function signatures, import/export details, and more
- 🚀 **PostMessage API** — can be embedded in other tools and driven via `window.postMessage`

---

## Getting Started

No build step or package installation required. The project runs entirely in the browser.

### Run locally

```bash
# Clone the repository
git clone https://github.com/<owner>/wasmcodeexplorer.git
cd wasmcodeexplorer

# Serve with any static file server
npx serve .
# or
python3 -m http.server 8080
```

Then open `http://localhost:8080` in your browser.

> ⚠️ You must use a local server — opening `index.html` directly as a `file://` URL will fail due to browser CORS restrictions on module loading.

### Usage

1. Click **Open File** and select a `.wasm` binary
2. The left panel shows the **hex dump** with color-coded byte groups
3. The right panel shows the **disassembly** (WAT format)
4. Click any byte group or instruction to highlight the corresponding element in the other panel
5. Hover over byte groups to see annotations (section type, operator details, etc.)

#### Loading a source map

To see original source lines alongside the disassembly:
- Click **Open File** again after loading a `.wasm` file
- Select the corresponding `.map` or source file

#### PostMessage API

The explorer can be embedded in an iframe and controlled programmatically:

```
# Open the explorer with the postmessage API enabled
http://localhost:8080?api=postmessage

# Send a wasm buffer to load
window.postMessage({ type: "wasmexplorer-load", data: { buffer: wasmBuffer } }, "*")

# Listen for the ready event
window.addEventListener("message", (e) => {
  if (e.data.type === "wasmexplorer-ready") { ... }
})
```

---

## Project Structure

```
wasmcodeexplorer/
├── index.html            # Main HTML — toolbar, hex dump panel, disassembly panel
├── viewer.js             # Core logic — wasm parsing, color mapping, hex rendering, click events
├── source-map-utils.js   # Source map parsing and display logic
├── heap.js               # Min-heap data structure (used internally by source map utils)
├── style.css             # Stylesheet
├── helloworld2.wasm      # Sample .wasm file for quick testing
└── LICENSE               # MPL-2.0
```

---

## Dependencies

Loaded via CDN — no `npm install` needed:

| Package | Purpose |
|---|---|
| [`wasmparser`](https://www.npmjs.com/package/wasmparser) | Parses `.wasm` binary format |
| [`wasmdis`](https://www.npmjs.com/package/wasmparser) | Disassembles wasm to WAT text format |
| [SystemJS](https://github.com/systemjs/systemjs) | Module loader |

---

## Color Reference

The hex dump uses these colors to distinguish wasm sections:

| Color | Section Type |
|---|---|
| Color 1 | Section header (`BEGIN_SECTION`) |
| Color 2 | Code operators / init expressions |
| Color 3 | Function body start / reloc section header |
| Color 4 | Wasm file header (`BEGIN_WASM`) |
| Color 5 | Exports, imports, type entries, function entries |
| Color 6 | Globals, memory, data, tables, elements |
| Color 7 | Name section entries, reloc entries |

---

## Contributing

Contributions are welcome! Here's how to get started:

1. Fork the repository
2. Create a new branch: `git checkout -b my-feature`
3. Make your changes
4. Open a Pull Request with a clear description of what you changed and why

### Areas open for contribution

- Fix the known scroll-sync bug (see `bug1154731b.png` and the `FIXME` comments in `viewer.js` and `source-map-utils.js`)
- Add support for newer WebAssembly proposals (SIMD, threads, etc.)
- Improve mobile/responsive layout
- Add more test `.wasm` sample files
- Modernize the module system (migrate from SystemJS to native ES modules)
- Improve annotation coverage for more section types

---

## License

[Mozilla Public License 2.0](./LICENSE)
