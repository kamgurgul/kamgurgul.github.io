---
title: Journey from Java to Kotlin Multiplatform
excerpt: "The story of the migration of the CPU Info application, which over the years has used most Android architectures and patterns."
tags: [android, kmp, java, kotlin, compose]
---

## CPU Info: A Personal Journey of My Project, Its Migrations, and Its Architectures

***Overview***

Welcome to my personal retrospective on **CPU Info**, an open-source tool I created to provide in-depth hardware and software details on a range of devices—from smartphones and wearables to desktops and TVs, even the web via WASM. In this blog post, I’d like to share how I started the project, the significant migrations it underwent, and the choice of technologies and architectural patterns I introduced along the way.

---

### 1. Early History & Origins

I launched **CPU Info** as a simple Android application to detect both hardware and software information on Android devices during development. My motivation came from wanting a deeper, hands-on understanding of how hardware and OS layers interact. Over time, I added features for GPU details, RAM usage, and temperature monitoring—turning the app into a go-to tool for enthusiasts who needed more capabilities than the default system settings.

These initial successes pushed me to expand the app beyond a single device type. While the early codebase was purely Android and reliant on Java, I began to imagine how CPU Info could serve different form factors and operating systems if the foundation was sufficiently modular.

---

### 2. Tools and key architectural shifts

TODO

---

### 3. Expansion to Additional Platforms

With CPU Info’s Android base solidified, I decided it was time to bring the same functionality to other devices. My solution was to leverage **Kotlin Multiplatform (KMP)** and reorganize the project into shared modules:

- **Data Interactors**: Shared logic for OS, memory, CPU, GPU, and sensor queries.
- **State Management**: AndroidX Lifecycle ViewModels and StateFlow for consistent patterns across platforms.
- **Common UI**: Compose usage for Android, Android TV, WearOS, iOS, Desktop, and WASM.

#### **Desktop (Linux, macOS, Windows)**
- By using Compose Multiplatform, I wrote a single UI layer that could run on desktop operating systems.
- For low-level system data, I turned to [OSHI](https://github.com/oshi/oshi) to handle hardware calls.

#### **iOS**
- I developed an iOS module with CPU metrics, battery info, and orientation data, bridging the gap between iOS-specific APIs and my shared KMP modules.

#### **WASM/Web**
- Deploying a web target required Compose support for WASM. Though hardware access is restricted in a browser, I still provided essential info like screen resolution, language data, and browser storage usage.

#### **Wear OS & Android TV**
- I created specialized UI flows for Wear OS and Android TV so users could quickly fetch device data on tiny watch displays or large TV screens.
- Shared logic among watch, TV, and phone versions minimized duplication and ensured consistent feature sets.

---

### 4. Challenges

1. **Feature Parity Across Platforms**: Creating a smooth user experience on iOS, Android, desktop, and Web forced me to write platform-specific providers for certain system-level details.
2. **Evolving Toolchains**: Early adopters of Compose Multiplatform faced frequent changes; I had to adapt quickly whenever APIs shifted.
3. **Full-Fledged CI**: Maintaining test coverage and build pipelines for so many targets was complex, but the end result was a stable, well-tested product.
4. TODO

---

### 5. Looking Ahead

I’m excited about what’s next:

- Deeper hardware detection.
- Possible additions like performance profiling and CPU benchmarking.
- Additional hardware targets (like Android Auto) or unusual form factors that can benefit from CPU Info’s robust detection capabilities.

---

### 6. Conclusion

TODO
