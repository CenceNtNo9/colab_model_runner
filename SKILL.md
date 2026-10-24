---
name: colab-model-runner
description: Deploy and run open-weight models on Google Colab, manage GPU sessions and Hugging Face downloads, and persist files in Google Drive. Use for llama.cpp, ComfyUI, inference, and generation tasks; not for offline-only ipynb editing.
---

# Colab Model Runner

Run the user's chosen model, verify at least one output, and provide a reusable way to start it. Preserve explicit choices of model, precision, GPU, interface, and storage location. If those choices are incompatible, explain the evidence before agreeing on an alternative.

## Choose an execution interface

Inspect existing connections first. Reuse a notebook or runtime belonging to the current task rather than allocating a duplicate GPU.

- If a connected Colab MCP or CLI supports the operation, prefer its structured execution, file transfer, and logging capabilities.
- Before using the official `google-colab-cli`, check its current version and platform support. The official documentation on 2026-10-25 supported Linux and macOS only; do not assume native Windows support. Do not install WSL or change the user's chosen environment without authorization.
- If only browser control is available, follow the browser skill available in the current environment. On the setup used for this skill, that is ego-browser; read its skill and version-specific guide first. See [browser operation](references/browser-operation.md) for relevant experience.

A skill provides operating instructions, not additional tool permissions or Google authorization. The user handles initial sign-in, Google Drive consent, and browser permission prompts. Continue authorized installation, downloads, execution, and routine fixes autonomously.

## Establish feasibility before deployment

1. Verify the official model ID, task type, and a credible inference implementation. Distinguish language, image, and audiovisual models.
2. Select an implementation using the GPU memory, system memory, and disk actually allocated. Budget for the main model, encoders, VAEs, runtime caches, and intermediate results, not just weight files.
3. Distinguish quantized weight storage from computation precision. For example, a compatible implementation can load some FP8 weights on an A100, but that does not establish native FP8 Tensor Core execution.
4. Read the current account's GPU, compute-unit balance, and hourly rate. Report the observed values as estimates at that time. Subscription benefits do not guarantee allocation of a particular GPU.

For llama.cpp, confirm compatible GGUF weights and a CUDA build path for the chosen model. For ComfyUI, confirm that the installed version includes the required nodes and inspect the component file layout. See the [validated MiniMax H3 case](references/minimax-h3.md) when relevant; do not copy its files, parameters, or nodes to unrelated models.

## Persistent storage and downloads

Use the user's existing project directory when available. Distinguish these locations:

- Google Drive: model backups, input assets, final outputs, workflows, deployment scripts, and run records.
- Colab `/content/`: software installations, runtime caches, and temporary working files.

After mounting Drive, verify the actual mount and project directory. Creating a similarly named local directory does not establish persistence. Limit operations to the current project's files unless the user requests otherwise; do not scan or modify unrelated Drive content.

Apply these principles to downloads and loading:

- Pin or record the Hugging Face repository, revision, required files, and quantization format. Download selected files rather than an entire large repository by default.
- For a first download, usually write to the runtime disk before backing up to Drive. Restore an existing complete Drive backup to the runtime disk for inference. Adjust the sequence using observed transfer performance.
- Check completed file sizes and verify publisher checksums when needed. Copy into temporary files and promote them after completion; preserve interrupted transfer state for recovery.
- If backup overlaps inference, distinguish "ready on the runtime disk" from "Drive backup complete." Wait for backups to finish and verify them before releasing the runtime.
- Configure the generator's final output directory under the project Drive directory, or automatically copy and verify outputs after each job. Do not promise automatic persistence for every file in unrelated temporary directories.

Where practical, download, install packages, upload assets, and prepare workflows during a CPU phase. Changing runtime type may discard temporary storage and processes; do not switch casually during transfers or generation.

## Execute and verify

Provide reusable steps in a clear order for installation, mounting, model preparation, startup, testing, and result access. Save scripts or a notebook, use absolute paths on the runtime, and record actual software versions.

When the user has not requested a graphical interface, Python, a CLI, or the local ComfyUI API can run the task. A working graphical interface is not a prerequisite for testing the model. Configure an interface when requested; do not publish an unauthenticated public service without authorization.

Run a representative short test first, fix random seeds where needed, and report key parameters such as precision, resolution, or token settings:

- Successful installation, an HTTP response, or a queued job does not establish successful generation.
- Check final job status, actual output files, and basic content. For media, inspect decodability, dimensions, duration, or audio tracks. For text, check that output is nonempty and satisfies the request's basic constraints.
- Identify the failing stage and node. If saving fails, inspect available caches or intermediate results before repeating inference unnecessarily.
- After a fix, update the persistent scripts and workflows as well as the temporary retry cell. Do not leave the reusable startup code broken.
- Notebook parameters, cell order, and explanations should describe the effective workflow. Mark optional unexecuted steps and unverified branches clearly.

## Finish and hand over

Save and verify final outputs, required model backups, scripts, and run records. Unless the user asks to keep the runtime allocated for this task, release it when the task finishes and verify that its session has stopped. Do not stop unrelated sessions. Closing a page, exiting a command, or seeing "not connected" does not by itself establish that the GPU has been released.

Report the task actually completed, output location, reusable entry point, persistence scope, whether the GPU is still consuming compute units, and limitations that affect later use. Provide accessible file or notebook links. Do not present a local script as though it has already been uploaded to Drive.

## Sources

- [Official Colab CLI and colab-operator](https://github.com/googlecolab/google-colab-cli)
- [Official Colab MCP](https://github.com/googlecolab/colab-mcp)
- [Colab FAQ](https://research.google.com/colaboratory/faq.html)
- [Hugging Face download guide](https://huggingface.co/docs/huggingface_hub/guides/download)

Consult the relevant current official documentation for plans, limits, model support, and installation parameters. This skill does not prescribe future rates or a permanent hardware catalog.
