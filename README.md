# GoWeb OS: Standalone Web Workspace Prototype

An architectural proof-of-concept demonstrating a lightweight, sandboxed, multi-window desktop environment engineered entirely inside a single, zero-dependency HTML5/Vanilla JS file. 

By eliminating the overhead of modern monolithic frontend frameworks, **GoWeb OS** achieves near-zero latency, execution immutability, and absolute predictable resource allocation.

---

## Architecture & Core Subsystems

The entire system is orchestrated by a low-level reactive kernel loop designed to simulate OS-level abstractions directly in the browser's execution context.

### 1. The Micro-Kernel (`Core Window Manager`)
* **Layering & Composition**: Dynamically intercepts mouse/touch input vectors to manage spatial hierarchy. It tracks active processes and mutates the hardware-accelerated CSS `z-index` stacking context on the fly, ensuring the focused process retains display hierarchy dominance.
* **State Encapsulation**: Windows are generated as isolated DOM sub-trees. Window control hooks (Minimize, Maximize, Close) are bounded via specific runtime handlers, eliminating inter-process memory leaks.
* **Input Rect Interception (Drag & Drop)**: Employs decoupled hardware-bound event listeners (`mousemove`/`mouseup`) with structural state caching to avoid layout thrashing during spatial translations.

### 2. High-Performance Graphics Subsystem
* **The HTML5 Canvas Matrix**: Embedded applications requiring rendering speed (such as the `G-Arcade Catch` engine) bypass standard DOM repaint stages entirely. 
* **State Synchronization**: Uses a deterministic runtime ticking loop (`setInterval` bound to the browser's refresh pipeline) executing raw pixel matrices on a dedicated Canvas rendering context. This architecture ensures solid 60 FPS performance regardless of DOM tree complexity.

---

## Production Use Cases

While packaged as a minimalist demonstration, the underlying paradigm offers massive utility for specialized infrastructure:

* **Isolated Kiosk Shells**: Ideal for secure, air-gapped terminal environments. Because the entire workspace is a single stateless file, the surface area for injection attacks is virtually non-existent.
* **Secure Intranet Nodes**: Perfect for deploying operational dashboards or internal system utilities inside highly restricted local corporate networks where third-party asset requests are heavily blocked.
* **Educational sandboxes**: Provides an un-abstracted, bare-metal environment for teaching low-level computer science concepts, memory space boundaries, event loops, and asynchronous JavaScript execution without framework noise.

---

## Extensibility & Production Scaling Options

The micro-weight profile of GoWeb OS carries immense latent scaling potential. The entire static runtime is intentionally designed to be replaced with dynamic, stateful subsystems:

### 1. In-Memory Virtual File System (VFS)
Currently, basic terminal shell commands (`ls`, `cat`) execute against static text arrays. In production, this data layout seamlessly hooks into browser storage primitives:
* **LocalStorage/IndexedDB Mapping**: The kernel can initiate a custom VFS map upon boot sequence. Commands like `mkdir`, `rm`, or `cat` will map directly to persistent key-value blobs or binary stores.
* **Zero-Trust Storage Isolation**: All data can be encrypted in-memory using the Web Crypto API before hitting the local physical persistence layer.

### 2. Cryptographic Integrity Boot Verification
The default kernel initialization log array (`const logs`) can be easily replaced by a true security routine:
* On boot, the system can calculate cryptographic SHA-256 hashes of the in-memory VFS structure or check external signed validation keys.
* If any unauthorized modification or side-channel manipulation is detected, the UI workspace can automatically trigger an execution fault state, refusing to clear the boot screen.

---

## Data Structures & App Integration Model

Adding custom software modules to GoWeb OS does not require refactoring the core kernel. The platform relies on a strict, declarative data model via the `appsConfig` registry. 

To expand the operating system's ecosystem, engineers simply inject a decoupled module object adhering to the following interface:

```javascript
const appsConfig = {
    your_app_key: {
        title: "Application Display Name",
        init: (contentId) => {
            const container = document.getElementById(contentId);
            
            // 1. Inject your UI Layout (Raw DOM or Canvas Viewport)
            container.innerHTML = `
                <div class="custom-app-wrapper">
                    <button id="${contentId}-action-trigger">EXECUTE</button>
                </div>
            `;
            
            // 2. Encapsulate your application logic isolated to this DOM tree
            setTimeout(() => {
                document.getElementById(`${contentId}-action-trigger`).onclick = () => {
                    console.log("App runtime isolated context executed successfully.");
                };
            }, 50);
        }
    }
};
```

Once declared in the registry, the micro-kernel automatically handles window lifecycle orchestration, window positioning drift offset, taskbar icon generation, and viewport visibility switching.

---

## License
Distributed under the MIT License. See `LICENSE` for more information.
