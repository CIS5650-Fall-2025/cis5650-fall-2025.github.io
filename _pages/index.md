---
layout: page
title: MatForge - Advanced Material Rendering System
description: >
  CIS 5650 GPU Programming Final Project - Implementing 4 SIGGRAPH papers (2023-2024) in a unified Vulkan path tracer
hide_description: true
permalink: /
---

![MatForge Banner](/assets/images/matforge_banner_temp.png){:class="img-projects"}

## Overview

**MatForge** is an advanced material rendering system that implements **FOUR complementary SIGGRAPH papers** (2023-2024) in a unified Vulkan ray tracing pipeline. Our goal is to create a production-quality material authoring and rendering workflow that combines state-of-the-art techniques for sampling, geometry, and shading.

### Project Context

- **Course**: CIS 5650 GPU Programming and Architecture
- **Semester**: Fall 2025
- **Institution**: University of Pennsylvania
- **Team**: 3 Students ([Yiding Liu](#team), [Cecilia Gao](#team), [Xiaonan Zhang](#team))
- **Timeline**: November 3 - December 7, 2025 (5 weeks)
- **Platform**: Vulkan 1.3 with ray tracing extensions
- **Base Framework**: Forked from [NVIDIA nvpro-samples/vk_gltf_renderer](https://github.com/nvpro-samples/vk_gltf_renderer)

---

## Features

### Rendering Pipeline Architecture

```
┌─────────────────────────────────────────────────────┐
│              MatForge Rendering Pipeline            │
├─────────────────────────────────────────────────────┤
│                                                     │
│  1. QOLDS - Sampling Foundation                     │
│     └─ Generate low-discrepancy random numbers      │
│        ↓                                            │
│  2. RMIP - Geometry Detail                          │
│     └─ Ray-trace displacement maps directly         │
│        ↓                                            │
│  3. Bounded VNDF - Direction Sampling               │
│     └─ Efficient importance sampling                │
│        ↓                                            │
│  4. Fast-MSX - BRDF Evaluation                      │
│     └─ Multiple scattering approximation            │
│        ↓                                            │
│  5. MONTE CARLO INTEGRATION                         │
│     └─ Combine: f(ωᵢ, ωₒ) × L(ωₒ) × cos(θ) / PDF    │
│                                                     │
└─────────────────────────────────────────────────────┘
```

### Implemented Techniques

#### ✅ Quad-Optimized Low-Discrepancy Sequences (QOLDS)
**Paper**: Ostromoukhov et al., ACM SIGGRAPH 2024
**Status**: Complete

- Base-3 Sobol' sequences with (1,4)-sequence property
- Owen scrambling for randomization
- 15-30% variance reduction vs. standard sampling
- Negligible performance overhead
- 47 dimensions × 243 max points (3^5)

**Benefits**: Foundation layer providing better random samples for all Monte Carlo integration tasks.

#### ✅ Fast Multiple Scattering Approximation (Fast-MSX)
**Paper**: ACM SIGGRAPH 2023
**Status**: Complete

- Relaxed V-cavity model for GGX materials
- Modified distribution and geometry terms
- Additive multi-scatter BRDF term
- 100× better energy conservation at high roughness
- ~5% performance overhead

**Benefits**: Fixes dark edges on rough materials, improves physical accuracy of GGX BRDF.

#### 🚧 RMIP (Rectangular MinMax Image Pyramid)
**Paper**: Thonat et al., ACM SIGGRAPH Asia 2023
**Status**: In Progress (GPU data structure complete, ray tracing integration underway)

- Tessellation-free displacement ray tracing
- Hierarchical min-max pyramid for oblong bounding
- Inverse mapping: ray ↔ texture space
- 11× faster than TFDM, 3× less memory than tessellation

**Benefits**: Add high-frequency geometric detail without mesh complexity.

#### 📋 Bounded VNDF Sampling
**Paper**: Eto & Tokuyoshi (AMD), ACM SIGGRAPH Asia 2023
**Status**: Planned for Milestone 2

- Tighter spherical cap bound for GGX sampling
- 15-40% variance reduction for rough surfaces (α = 0.6-1.0)
- Minimal overhead (<1%)

**Benefits**: Fewer rejected samples for rough materials, faster convergence.

---

## Gallery

### Milestone 1 Progress

![QOLDS Implementation](/assets/images/gallery/qolds_screenshot.png)
*QOLDS sampling integration with GUI toggle and console logging*

![RMIP Structure](/assets/images/gallery/rmip_structure.png)
*RMIP hierarchical min-max pyramid structure*

![Fast-MSX Showcase](/assets/images/gallery/msx_showcase.png)
*Fast-MSX multi-scattering approximation improving energy conservation*

---

## Team

| Team Member | Role | Technique | Status |
|-------------|------|-----------|--------|
| **Yiding Liu** | Sampling Specialist | Quad-Optimized LDS | ✅ Complete |
| **Cecilia Gao** | Geometry Specialist | RMIP Displacement | 🚧 In Progress |
| **Xiaonan Zhang** | Material Specialist | Fast-MSX + Bounded VNDF | ✅ Fast-MSX Complete |

---

## Milestones

### Milestone 1: November 12, 2025 ✅
**Goal**: Individual techniques working (foundations)

**Achievements**:
- ✅ QOLDS fully integrated (700 LOC)
- ✅ RMIP GPU data structure builder (800 LOC)
- ✅ Fast-MSX implementation (350 LOC)
- ✅ Total: ~1,850 lines of production code

[View Milestone 1 Report →](https://github.com/matforge/MatForge/blob/master/doc/presentations/Milestone1.md)

### Milestone 2: November 24, 2025 🎯
**Goal**: Full pipeline integration + material system

**Target Deliverables**:
- Complete RMIP ray tracing integration
- Bounded VNDF implementation
- Material library (7+ materials)
- Performance benchmarks
- Comparison modes (toggle each technique)

### Final Presentation: December 7, 2025 📅
**Goal**: Production features + comprehensive analysis

**Final Deliverables**:
- Complete 4-technique pipeline
- Material parameter editor
- Demo video
- Technical documentation
- Performance analysis

---

## Documentation

### Quick Links

- [GitHub Repository](https://github.com/matforge/MatForge)
- [README.md](https://github.com/matforge/MatForge/blob/master/README.md) - Quick start and build instructions
- [Milestone 1 Report](https://github.com/matforge/MatForge/blob/master/doc/presentations/Milestone1.md)


### Papers

1. **Quad-Optimized LDS**: Ostromoukhov et al., "Quad-Optimized Low-Discrepancy Sequences", ACM SIGGRAPH 2024
2. **RMIP**: Thonat et al., "Displacement ray-tracing via inversion and oblong bounding", ACM SIGGRAPH Asia 2023
3. **Bounded VNDF**: Eto & Tokuyoshi, "Bounded VNDF Sampling for Smith-GGX Reflections", ACM SIGGRAPH Asia 2023
4. **Fast-MSX**: "Fast Multiple Scattering Approximation", ACM SIGGRAPH 2023

---

## Performance Targets

| Resolution | Quality | Target FPS | Hardware |
|------------|---------|------------|----------|
| 1080p | Interactive | 60 | RTX 4070 |
| 1080p | Balanced | 30 | RTX 4070 |
| 1440p | Quality | 20 | RTX 4070 |

**Expected Improvements**:
- QOLDS: 15-30% variance reduction
- RMIP: 11× faster than tessellation
- Bounded VNDF: 15-40% fewer rejected samples
- Fast-MSX: 100× energy conservation improvement

---

## Build Instructions

### Quick Start

```bash
# Clone repository
git clone https://github.com/matforge/MatForge.git
cd MatForge

# Configure and build (Windows)
cmake -B build -S . -DUSE_DLSS=ON
cmake --build build --config Release

# Run
.\_bin\Release\vk_gltf_renderer.exe
```

**Requirements**:
- Windows 10/11
- NVIDIA RTX GPU (RTX 20-series or newer)
- Vulkan SDK 1.3+
- CMake 3.25+
- C++20 compiler (MSVC 2022 / GCC 11+)

For detailed build instructions, see [README.md](https://github.com/matforge/MatForge/blob/master/README.md).

---

## Acknowledgments

- **Instructor**: Shehzan Mohammed (University of Pennsylvania)
- **Base Framework**: [NVIDIA nvpro-samples](https://github.com/nvpro-samples/vk_gltf_renderer)
- **Papers**: SIGGRAPH 2023-2024 authors
- **Course**: [CIS 5650 GPU Programming](https://cis5650-fall-2025.github.io/)

---

## Contact

- **GitHub**: [matforge/MatForge](https://github.com/matforge/MatForge)
- **Course Forum**: [Ed Discussion](https://edstem.org/us/courses/81464/discussion)
- **LinkedIn**: [CIS 5650 Alumni Group](https://www.linkedin.com/groups/6540935/)

---

*Last Updated: November 18, 2025*
