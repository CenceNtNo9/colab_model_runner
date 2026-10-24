# MiniMax H3: Validated Single-GPU Case

This records a case run on 2026-10-25. It is not a fixed configuration for other models or future versions.

## Observed run

- GPU: NVIDIA A100-SXM4-40GB; approximately 83.5 GB of system memory.
- Python 3.13.16, PyTorch 2.11.0+cu130, ComfyUI 0.42.0.
- Task: text-to-audiovisual generation with the FL2VA model and no reference assets.
- Main H3 model: pruned FP8 scaled; text encoder: NVFP4-AWQ; video VAE: FP16; audio VAE: FP32.
- On the A100, logs listed FP8 and NVFP4 as emulated operations, with BF16 computation for the main model. Do not describe the entire pipeline as FP8 computation.
- Parameters: 864×480, 124 frames, 24 FPS, 20 steps, `res_multistep` / `simple`, seed 20261025.
- Actual output: an MP4 containing video and audio streams, 534,323 bytes.

## Weight manifest

Repository: `Comfy-Org/MiniMax-H3`. Revision used: `e5eb578a89295337b8ff433a035929ce0279e0b6`. Approximately 42.46 GB in total.

| Relative path | Bytes |
| --- | ---: |
| `diffusion_models/minimax_h3_fl2va_pruned_fp8_scaled.safetensors` | 20,958,205,608 |
| `text_encoders/qwen3vl_32b_minimax_h3_nvfp4_awq.safetensors` | 15,687,142,551 |
| `vae/minimax_h3_video_vae_fp16.safetensors` | 5,207,808,496 |
| `vae/minimax_h3_audio_vae_fp32.safetensors` | 605,254,808 |

Use this manifest only when the user explicitly wants to reproduce this case. Pruned weights are not merely a dtype conversion of the full BF16 weights. Full-reference Ref2VA mode requires a different main model and corresponding nodes; do not download it incidentally for an initial text-only test.

## ComfyUI details

The service listened on a local port, used `--lowvram --reserve-vram 4`, and mapped input and output directories to the project Drive directory. The background process used a separate process session to avoid unintended termination when interrupting the notebook. Finishing the task still required releasing the entire Colab runtime.

The main graph used `UNETLoader`, `CLIPLoader(type="minimax")`, two `VAELoader` nodes, `MiniMaxH3ImageToVideo`, `SamplerCustomAdvanced`, video and audio decoding, `CreateVideo`, and `SaveVideo`.

**The validated SaveVideo API argument was `format="mp4"`.** Passing a nested dictionary for that field in 0.42.0 allowed the job to be queued but failed at SaveVideo with a missing `format` argument. Resubmitting the corrected graph reused the upstream cache and saved the result successfully. Query `/object_info/SaveVideo` for the version in use; do not assume UI widget values translate into nested API objects.

## Persistent locations for this project

Drive root: `My Drive/NewGalTry/`.

- `models/MiniMax-H3/`: the four weight files and `newgaltry_manifest.json`.
- `scripts/`: `download_models.py`, `start_comfyui.py`, `test_h3.py`, `upload_reference.py`, `download_result.py`, and usage notes.
- `workflows/`: the test graph, final job history, and `deployment_result.json`.
- `output/H3_test/NewGalTry_A100_FP8_00001_.mp4`: the verified output.

On every resume, confirm that files still exist, Drive is mounted, and the expected session is available. The A100 was released after this test; its runtime address is not a reusable entry point.

Sources: [model files](https://huggingface.co/Comfy-Org/MiniMax-H3), [official ComfyUI tutorial](https://docs.comfy.org/tutorials/video/minimax/minimax-h3), [ComfyUI source](https://github.com/Comfy-Org/ComfyUI).
