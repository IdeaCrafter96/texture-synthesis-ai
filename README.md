# AI-Assisted Procedural Texture Generation (Research Project)

This project explores whether a real-world texture can be approximated by a procedural material and a shader program, instead of storing the original bitmap.  
The idea is inspired by procedural material workflows (Substance Designer, node-based graphs) and by recent work on neural texture compression (e.g., SIGGRAPH 2023), but the goal here is different:  
**generate a human-readable procedural graph + shader code.**

This is still early-stage research. Some parts work well, some are experimental, and some need more investigation — especially shader cost evaluation.  
I’m sharing this publicly because I’d like feedback from graphics programmers, shader authors, and anyone familiar with material pipelines.

---

## Why this project exists

Modern games and cloud-rendered applications move a lot of texture data around.  
In multi-region deployments or cloud gaming backends, texture storage and distribution can become a bottleneck.  
Procedural materials don’t solve everything, but they *might* reduce:

- storage footprint  
- distribution overhead  
- cold-start latency  
- texture streaming complexity  

The trade-off is obvious: **procedural shaders cost GPU time**.  
This project tries to explore that trade-off in a structured way.

---

## What the system actually does

Given an input texture, the system attempts to:

1. **Analyze visual structure**  
   Identify patterns, frequencies, periodicity, and multi-scale features.

2. **Generate a procedural graph**  
   A node-based graph composed of noise functions, filters, blends, transforms, etc.  
   (Think: a simplified Substance Designer graph.)

3. **Tune parameters**  
   Adjust noise scales, blend factors, thresholds, distortions, etc.

4. **Optionally generate a fallback texture layer**  
   Used when the procedural graph cannot fully reconstruct the material.

5. **Generate shader code**  
   Output GLSL/HLSL/WGSL that implements the procedural graph.

6. **Evaluate reconstruction quality**  
   Compare the procedural output to the original texture.

This is not meant to replace artist-created materials.  
It’s an experiment in automated proceduralization.

---

## Example (simplified)

A brick texture might produce a graph like:

- Base noise → Worley  
- Edge mask → Sobel-like filter  
- Color variation → Perlin FBM  
- Blend → Multiply + Overlay  
- Distortion → Simplex warp  
- Fallback → low-res residual texture

Shader output is a single function that reconstructs the material using these operators.

---

## Shader cost (open problem)

This is the part I’m actively researching and where I’d appreciate help.

Questions I’m trying to answer:

- How do you estimate the cost of a procedural material shader?  
- What heuristics do graphics programmers use in production?  
- When does a procedural shader become “too expensive”?  
- How do you compare ALU-heavy shaders vs texture fetches?  
- How do engines typically measure shader complexity?

If you have experience with this, I’d love to hear your thoughts.

---

## Graphics Notes (for shader/material authors)

Some terminology translated into graphics-friendly language:

- **Procedural graph**  
  A node-based material graph (noise, blends, filters).  
  Similar to Substance Designer or Unreal’s material editor.

- **Residual layer**  
  A fallback texture used when the procedural graph cannot fully reconstruct the material.  
  Blended in the shader.

- **Operator**  
  A noise function, filter, blend mode, warp, etc.

- **Cost model**  
  Rough estimate of ALU ops, texture fetches, branches, and GPU time.

---

## Limitations (honest list)

- Some materials are too complex to proceduralize.  
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
- Better residual blending  
- Benchmarking on real engines  
- Integration tests with Unity/Unreal custom nodes

---

## FAQ

**Q: Is this meant for production?**  
No — this is research.

**Q: Does it replace artists?**  
Absolutely not. It’s a technical experiment.

**Q: Why procedural instead of neural decoding?**  
Procedural shaders are portable, readable, and engine-friendly.

**Q: Why not compress textures instead?**  
Compression helps storage, not distribution or multi-region deployment.

**Q: Does it work for all materials?**  
No — some materials are too irregular.

**Q: Do you have examples of the generated shaders?**  
Yes — I’m adding them gradually. The generated shaders are readable and structured as a single function that reconstructs the material from the operators. I’ll include short examples in the repository so the ALU/TEX balance is easy to inspect.

**Q: What exactly is the “residual layer”?**  
It’s essentially a fallback texture. When the procedural graph can’t fully reproduce the fine details of the original material, the system generates a small residual texture that gets blended in the shader.

**Q: How large do the procedural graphs get for complex materials?**  
Simple materials (concrete, sand, metal) usually produce 5–12 operators.  
More complex ones (wood, brick, cloth) can reach 20+.  
Graph simplification is planned to reduce unnecessary nodes.

**Q: How does the system decide when to use the residual layer?**  
Based on reconstruction error. If the difference between the procedural output and the original texture exceeds a threshold, a fallback texture is generated automatically.

**Q: How do you evaluate reconstruction quality?**  
I use SSIM, perceptual metrics, frequency analysis, and visual side‑by‑side comparisons. Visual examples will be added to the README.

**Q: How do you estimate shader cost?**  
Right now: ALU instruction count, number of texture fetches, branching, ALU/TEX balance, and heuristic GPU time estimates.  
This is the part where I’m actively looking for feedback from graphics programmers.

**Q: How does a procedural shader compare to a traditional texture?**  
Procedural shaders trade texture fetches for ALU work.  
They make sense when storage/bandwidth is expensive and shader cost is acceptable.  
Fallback textures help when the procedural version becomes too expensive.

**Q: How is the procedural graph generated?**  
By analyzing frequency content, periodicity, local structures, color variation, and noise characteristics. Operators are selected based on these features, and their parameters are optimized.

**Q: How do you handle tiling?**  
Most procedural operators (Perlin, Worley, Simplex) are tile‑friendly by nature.  
Residual textures are generated as seamless variants.

**Q: What about mipmapping and LOD?**  
The procedural part doesn’t require mipmapping.  
The fallback texture uses a standard mip chain.  
LOD heuristics are planned for future versions.

**Q: Can this run inside a real engine?**  
Yes — the output is compatible with GLSL, HLSL, and WGSL.  
Engine integration would require a wrapper node and a sampler for the fallback texture.

**Q: Does this work for all materials?**  
No — highly irregular materials are still difficult to proceduralize.  
That’s why the fallback layer exists.

**Q: Why procedural instead of neural decoding?**  
Procedural shaders are portable, readable, engine‑friendly, and require no model inference.

**Q: What’s planned next?**  
A better shader cost model, more operators, graph simplification, engine integration, and benchmarking.

---

## Full Proposal

The full 17-page proposal is available in the repository.  
It includes architecture details, evaluation notes, and examples.

