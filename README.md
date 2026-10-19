# AI-Assisted Procedural Texture Generation (Research Project)

This project explores whether a real-world texture can be approximated by a procedural material and a shader program, instead of storing the original bitmap.  
The system attempts to generate a human-readable procedural graph and a shader that reconstructs the material using noise functions, filters, blends, and optional fallback layers.

This is early-stage research. Some parts work reliably, others are experimental, and shader cost evaluation is still an open problem.  
The project is public because feedback from graphics programmers, shader authors, and engine developers is extremely valuable.

---

## Motivation

Modern cloud-rendered applications and game streaming platforms move large amounts of texture data across regions.  
In multi-region deployments, texture storage, replication, and distribution can become more expensive than GPU compute itself.

Procedural materials don’t eliminate the need for textures, but they *might* reduce:

- storage footprint  
- cross-region distribution overhead  
- cold-start latency  
- texture streaming complexity  

The trade-off is clear: **procedural shaders increase ALU cost**.  
This project explores that trade-off and tries to understand when procedural reconstruction becomes too expensive compared to traditional texture fetches.

---

## System Overview

Given an input texture, the system attempts to:

1. **Analyze visual structure**  
   Identify frequencies, patterns, periodicity, and multi-scale features.

2. **Generate a procedural graph**  
   A node-based graph composed of noise operators, filters, blends, transforms, etc.

3. **Tune parameters**  
   Optimize noise scales, blend factors, thresholds, distortions, and other operator settings.

4. **Optionally generate a fallback residual texture**  
   Used when the procedural graph cannot fully reconstruct the material.

5. **Generate shader code**  
   Output GLSL/HLSL/WGSL code that implements the procedural graph.

6. **Evaluate reconstruction quality**  
   Using SSIM, perceptual metrics, frequency analysis, and visual comparison.

This is not intended to replace artist-created materials.  
It is a technical experiment in automated proceduralization.

---

## Shader Cost (Open Research Area)

The main open question is how to evaluate the cost of procedural shaders in a way that is meaningful for real engines and cloud environments.

Topics currently under investigation:

- ALU vs memory bandwidth trade-offs  
- When procedural shaders outperform texture fetches  
- When they become too expensive  
- How engines typically measure shader complexity  
- How to approximate cost without full-resolution evaluation  
- Hybrid approaches (procedural generation + cached texture)

Feedback from graphics programmers is especially welcome.

---

## Limitations

- Some materials are too irregular to proceduralize.  
- Generated graphs can be larger than expected.  
- Shader output is not always optimal.  
- Residual blending is still experimental.  
- No anisotropic filtering support yet.  
- No LOD switching logic yet.  
- No mipmapping for fallback textures.

---

## Future Work

- Better shader cost modeling  
- More operators (Gabor noise, cellular patterns, etc.)  
- Graph simplification  
- Improved residual blending  
- Benchmarking on real engines  
- Integration tests with Unity/Unreal custom nodes

---

## Full Proposal

The full proposal (Version 2) is available in the repository.

### **Serbian version (PDF & DOCX)**  
Located in:  docs/sr/v2/


### **English version**  
Planned for: 1st of November
Version 1 is available in docs/en/v1/

