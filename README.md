# Official Happy Horse AI Repository

**Disclaimer:** *There is talk in the AI community of the Happy Horse model being a hoax or vaporware. However, I wanted to share all the technical details and claims I've gathered here in one place, and I will keep updating this repository as I find out more.*

Happy Horse 1.0 is described as an open-source, state-of-the-art AI video generator with native joint audio-video generation. This means the model claims to produce video frames and the corresponding audio track (dialogue, ambient sound, Foley) together in a single forward pass, rather than generating silent video and dubbing it afterward.

## 🐎 Model Architecture & Claims

According to community-compiled architecture notes and alleged technical leaks, the model is built around a 15-billion-parameter unified self-attention Transformer.

| Component | Reported Specification |
| :--- | :--- |
| **Total parameters** | ~15B |
| **Architecture** | Unified self-attention Transformer (no dedicated cross-attention branches) |
| **Total layers** | 40 |
| **Layer layout** | Sandwich — first 4 & last 4 layers use modality-specific projections; middle 32 share parameters across all modalities |
| **Modalities** | Text, image, video, and audio tokens — concatenated into a single sequence |
| **Multimodal fusion** | Per-attention-head learned scalar gates with sigmoid activation |
| **Timestep handling** | No explicit timestep embeddings — infers denoising state directly from noise level |
| **Distillation** | DMD-2 (Distribution Matching Distillation v2) |
| **Sampling steps** | 8 |
| **Classifier-free guidance** | Not required |
| **Reference GPU / Speed** | NVIDIA H100 80GB (Reportedly ~38s for 1080p generation) |

## 🧠 Deep Dive into the Reported Architecture

### Unified Self-Attention vs. Cross-Attention
Most open-source video models (like Wan 2.2, HunyuanVideo, LTX-2) use a Diffusion Transformer (DiT) backbone where text conditioning is injected via cross-attention, and audio is handled by entirely separate models. Happy Horse 1.0 reportedly relies entirely on unified self-attention, putting text, image, video, and audio into the same sequence and letting attention handle the alignment natively.

### Sandwich Layer Layout
The first 4 and last 4 layers allegedly handle modality-specific embedding and decoding, while the middle 32 layers share parameters across all modalities. This parameter efficiency means most of the network performs cross-modal reasoning instead of being split into siloed sub-networks.

### Per-Head Sigmoid Gating
Joint multimodal training is notoriously unstable because gradients from the audio loss can dominate (or be dominated by) gradients from the video loss. The purported solution is a learned scalar gate on each attention head, selectively dampening heads that produce destructive gradients.

### DMD-2 Distillation & Timestep-Free Denoising
Standard video diffusion typically requires 25–50 sampling steps with classifier-free guidance (CFG), driving up inference costs. Happy Horse claims to use DMD-2 (Distribution Matching Distillation v2) to train a student model to match the teacher's output in just 8 steps with no CFG. It also allegedly omits explicit timestep embeddings, deducing the noise level directly from the noisy latents.

## 📊 How It Stacks Up (On Paper)

Here is how the reported specifications compare to current open-weights leaders:

| Feature | Happy Horse 1.0 (Claims) | LTX-2 Pro | Wan 2.2 A14B |
| :--- | :--- | :--- | :--- |
| **Parameters** | ~15B | ~13B | 14B |
| **Backbone** | Unified self-attention | DiT | DiT |
| **Native Audio** | ✅ Joint with video | ❌ | ❌ |
| **Sampling Steps** | 8 (no CFG) | ~25 | ~50 |
| **1080p Time** | ~38s on H100 | Minutes | Minutes |
| **Open Weights** | ❌ **Not Released** | ✅ Available | ✅ Available |

## 🗣️ Community Sentiment & The "Hoax" Rumors

The Happy Horse AI video generator first surfaced publicly as a "mystery model" on the Artificial Analysis Video Arena, competing anonymously against frontier models. 

However, because the weights, inference code, and official technical reports have **not** been released, there is growing skepticism. The claims of native lip-syncing in 6+ languages, combined with the blazing fast 8-step inference for 1080p video, are incredibly ambitious. Many in the AI community suspect the model might be a hoax, a wrapper around an existing proprietary API, or vaporware. 

I am tracking this closely. If and when actual weights, code, or verified technical papers drop, I will update this repository immediately.

## 🎥 Video Examples

You can find downloaded sample videos in the `examples/` directory of this repository. Please note these are shared for reference and have been circulating online as purported outputs of the model.
