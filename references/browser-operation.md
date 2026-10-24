# Browser Operation Notes

Read this reference only when controlling Colab through a browser. Follow the current browser skill for tool signatures; these notes do not replace its guide.

## Windows and ego-browser

- Use one task space for the goal and resume it across rounds. Do not create a new space merely to retry.
- In PowerShell, write multiline or non-ASCII JavaScript to a UTF-8 `.mjs` file and execute it through an ASCII `file:///...` import entry point. Use the available file tools to avoid Windows PowerShell 5.1 encoding and argument-quoting problems.
- After handing control to the user, wait until they explicitly request continuation or confirm that authorization is complete, then resume according to the tool guide. Do not bypass user control.
- Prefer snapshots and semantic selectors for ordinary DOM content. The Colab editor's hidden textarea may reject clicking or focus. After inspecting the current screenshot, click the visible code area and paste code if needed. Verify a short cell before repeating edits at scale.
- Inserting or deleting cells changes their displayed indices. Locate the target again; do not treat a previous index, ref, or Monaco model URI as a persistent ID.

## Reduce browser round trips

Use bounded, purposeful cells for installation, file preparation, or a test, and save reusable scripts. Use a supported file-transfer interface when available rather than routinely placing large encoded payloads in notebook cells.

Read only the relevant output regions. Colab snapshots may truncate long output lines. When needed, print diagnostics as short fields, inspect an output screenshot, or download the current task's log. Truncated text does not establish that a success marker was absent.

The browser terminal is useful for temporary diagnostics. Prefer recorded Python cells or connected execution tools for automation. If the terminal does not receive pasted or typed text, inspect the screen before retrying. Confirm focus and shortcut behavior, then choose a supported alternative such as individual key presses and verify the result.

## Port interfaces and callbacks

Programmatic ComfyUI clients can request `http://127.0.0.1:8188` from the same runtime without first opening the graphical interface.

Colab provides `output.serve_kernel_port_as_iframe`. Decouple interface loading from Python backend work so an unreturned frontend callback cannot block the task indefinitely. If `output.eval_js` is necessary, set a reasonable `timeout_sec` and handle timeout. Do not repeatedly reload a page while waiting for a long job.

When reconnection occurs, distinguish the page connection, Python kernel, background inference process, and runtime allocation. Interrupting a waiting Python cell may also signal child processes that were not isolated. Check the service again after recovery instead of relying on an earlier "started successfully" message.

## Finish

Save and verify Drive files before using Colab's "Disconnect and delete runtime" to release this task's resources. Check active sessions and the hourly rate in Resources or session management. Closing a tab is not a substitute for releasing the GPU.

At the end of browser work, follow the current tool guide to clean up intermediate tabs created for this task. Retain only the notebook or result pages the user needs to view or continue using.
