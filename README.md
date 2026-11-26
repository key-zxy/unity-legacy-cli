# Unity Legacy CLI

Control a running Unity Editor via a local, Editor-only Unity package. The bridge works on the Unity Editor thread and exchanges JSON requests through the project's `Library/UnityCli` directory. There is no port or service to configure.

## Compatibility

| Unity version | Validation and feature notes |
| --- | --- |
| 2020.3.45f1, Windows | Clean-project Editor compilation and CLI end-to-end coverage, including finite-value validation, bounded UI Toolkit inspection and atomic UXML/USS writes. |
| 2022.3.21f1, Windows | Experimental. Clean-project Editor compilation, status, batch reads and scene creation passed. The full editing and capture workflow timed out. |
| 2019.4 LTS and 2020.1–2020.2 | Experimental compatibility target. These Editor versions have not been tested end to end. Prefab Mode uses the version-appropriate API. Profiler counter enumeration requires 2020.2 or later. Material writes require Unity's single-asset save API. |
| Earlier than 2019.4 | Outside the package's declared minimum. |

Other Editor patches, Unity 6, macOS and Linux are unverified. Feature availability can vary with the Editor patch, graphics pipeline and installed platform modules. Unity itself must be installed and licensed. On Unity versions that show an API update prompt, resolve it in the Editor and wait for compilation before using the CLI.

## Install

Requires Python 3.10+ and an open Unity project. Install the wheel from GitHub Releases, then locate the bridge.

```console
python -m pip install https://github.com/key-zxy/unity-legacy-cli/releases/download/v0.3.2/unity_legacy_cli-0.3.2-py3-none-any.whl
unity-legacy-cli bridge-path
```

Open **Window → Package Manager** in Unity, choose **Add package from disk**, and select the printed `package.json`. Keep the Python installation at that path while the Unity project uses it. Unity resolves the official Newtonsoft JSON, uGUI and Test Framework packages declared by the bridge. The Editor bridge stays out of player builds.

```console
unity-legacy-cli --project /path/to/UnityProject --json status
unity-legacy-cli --project /path/to/UnityProject --json scene hierarchy --limit 50
unity-legacy-cli --project /path/to/UnityProject --json console read --type error
```

From within a Unity project, omit `--project` to use the nearest project root. Place global options such as `--json` and `--timeout` before the command. The REPL opens when run without a command in an interactive terminal.

From a Git checkout, use `python -m pip install .`. The Unity package ID remains `com.cli-anything.unity2020` for compatibility with the original CLI-Anything contribution. If a project already has that bridge installed, it can use the new command with the existing bridge at the same version.

## Supported operations

| Command groups | Coverage |
| --- | --- |
| `editor`, `console` | State, Play Mode, Undo/Redo, menu calls, refresh, compilation and visible Console entries. |
| `scene`, `gameobject`, `component` | Scene lifecycle and hierarchy, GameObject and primitive editing, component inspection and typed property changes. |
| `asset`, `prefab` | Asset lookup, dependencies, import and move, Prefab inspection and creation, Prefab Mode. Batch commands add generic UI Prefab inspection, detached Prefab edits and Sprite import settings. |
| `camera`, `ui` | Camera and uGUI inspection and editing, camera PNG capture, UXML/USS asset inspection and creation. |
| `script`, `scriptable-object` | Source inspection/search, hash-checked script edits, ScriptableObject discovery and creation. Script validation checks structure. Unity compilation checks syntax and types. |
| `test`, `build`, `package` | Unity Test Framework jobs, build configuration/jobs and Package Manager list/add/remove jobs. |
| `profiler`, `graphics`, `material`, `particle`, `animator`, `physics`, `primitive` | Profiler control, rendering settings, materials, particle and Animator inspection, physics queries and primitive creation. |
| `batch` | Run 1–25 commands in order. Stop at the first failure and return results so far. Earlier writes stay applied. |

Use each group's `--help` for its flags. A successful `requested: true` acknowledges editor actions that finish later. Query status and the Console afterward. For jobs, poll the corresponding `job` command. Camera PNGs capture scene contents through Unity's renderer. Editor-window screenshots, runtime UI Toolkit documents, Frame Debugger captures and Memory Profiler snapshots are outside this release's coverage. Build and package commands depend on the selected platform modules and current project state.

Only run commands and open projects you trust. Processes with write access to the project's bridge directory can request Editor operations. Confirm before running Play Mode or Play Mode tests on a user's project.

## Test

```console
python -m pytest cli_anything/unity_legacy/tests/test_core.py -v
```

Set `CLI_ANYTHING_UNITY_PROJECT` to a disposable Unity project with the bridge installed and the Editor running in Edit Mode. Run `test_full_e2e.py` with `CLI_ANYTHING_FORCE_INSTALLED=1` to require the installed executable. The test writes scenes, prefabs and PNGs in the specified project.

See the [bridge architecture](ARCHITECTURE.md) and [test results](cli_anything/unity_legacy/tests/TEST.md).

## Credits

Maintained by [key-zxy](https://github.com/key-zxy). This standalone release comes from the [CLI-Anything Unity contribution](https://github.com/HKUDS/CLI-Anything/pull/715) and uses that project’s Apache-2.0-licensed REPL skin.
