# Test plan

- `test_core.py` verifies packaged bridge discovery, project selection, closed-Editor failure, timeout cleanup and request/response isolation without Unity.
- `test_full_e2e.py` uses the installed CLI and a running clean Unity project. The workflow creates a scene with a camera, checks Console and build metadata, saves a Prefab, renders a PNG through Unity and verifies its signature and dimensions. It also verifies that a failed command prevents later commands in a batch, that non-finite component and shader values are rejected without changing state, and that UI Toolkit folder inspection is bounded while paired UXML/USS creation preflights paths and rolls back the first file when the second write fails.
- Set `CLI_ANYTHING_UNITY_PROJECT` to a disposable Unity project already running with the packaged bridge.

## Results

The standalone v0.3.2 wheel was built from the repository and installed into an isolated test environment. The test project manifest specified only the public bridge installed from that wheel, which resolved its declared Unity packages. The tests ran outside the source checkout against the installed package and executable.

Unity 2020.3.45f1 on Windows, installed-command mode

```text
test_packaged_bridge PASSED
test_project_resolution PASSED
test_editor_closed PASSED
test_timeout_leaves_no_request PASSED
test_concurrent_requests_are_isolated PASSED
test_batch_reads_and_stops_at_failure PASSED
test_edit_save_and_render_in_unity PASSED
test_edit_rejects_non_finite_values PASSED
test_ui_toolkit_inspect_is_bounded_and_create_is_atomic PASSED
9 passed (test_core.py 5 passed in 0.40s; test_full_e2e.py 4 passed in 11.33s)
```

The v0.3.1 validation in Unity 2022.3.21f1 on Windows compiled the bridge and passed batch reads and scene creation in a clean project; the next GameObject creation request timed out, so the 2022.3 render workflow remains unverified. The v0.3.2 fixes were compiled and tested only on Unity 2020.3.45f1. Play Mode, actual player builds, package mutations and versions earlier than 2020.3 were outside this validation run.
