# EVA

**EVA is a compact, single-file Python agent for command-line work.** It can use an OpenAI-compatible LLM endpoint to help with tasks such as writing scripts, running commands, analysing data, and working with code.

EVA keeps a conversation session for each working directory and asks for approval before running commands that its safety review does not classify as read-only. It can use a hosted API or a compatible local model.

> [!WARNING]
> EVA can run shell commands in your current working directory. Review every command before approving it. The `--allow-all` option disables the approval step and should only be used in an environment you fully control.

[🇨🇳 中文](./README.md) · The original Chinese documentation is available in [README.md](./README.md).

## Requirements

- Python 3. The base program uses only the Python standard library.
- An API key for an OpenAI-compatible chat-completions endpoint.
- An endpoint that provides both:
  - `GET <EVA_BASE_URL>/models`, returning a `data` list containing the configured model ID; and
  - `POST <EVA_BASE_URL>/chat/completions`.

EVA checks the model list during startup, so `EVA_API_KEY`, `EVA_BASE_URL`, and `EVA_MODEL_NAME` must be set before running any EVA command, including `--help`.

### Current language limitation

EVA's built-in system prompts, command-review prompt, terminal messages, and error messages are currently written in Chinese. A multilingual model can run EVA, but it may answer in Chinese even when prompted in English.

Optional enhancements:

```bash
python3 -m pip install prompt_toolkit rich
```

- `prompt_toolkit` enables multi-line input in an interactive terminal: `Enter` submits and `Ctrl+N` inserts a new line. `Alt+Enter` may also work, depending on the terminal.
- `rich` renders the final response as Markdown and styles model reasoning output.

## Quick start

Clone the repository and enter it:

```bash
git clone https://github.com/usepr/eva.git
cd eva
```

Configure an OpenAI-compatible provider. Replace the placeholder values with the endpoint, model ID, and API key that you intend to use. `EVA_BASE_URL` should be the API base path without a trailing slash.

### Linux and macOS

```bash
export EVA_BASE_URL="https://your-api-host.example/v1"
export EVA_MODEL_NAME="your-model-id"
export EVA_API_KEY="your-api-key"
python3 eva.py
```

### Windows Command Prompt

```cmd
set EVA_BASE_URL=https://your-api-host.example/v1
set EVA_MODEL_NAME=your-model-id
set EVA_API_KEY=your-api-key
python eva.py
```

### Windows PowerShell

```powershell
$env:EVA_BASE_URL="https://your-api-host.example/v1"
$env:EVA_MODEL_NAME="your-model-id"
$env:EVA_API_KEY="your-api-key"
python .\eva.py
```

### Example: OpenRouter

The following OpenRouter configuration was verified for startup and a basic interactive conversation. Use your own OpenRouter API key; do not paste it into source files or commits.

```bash
export EVA_BASE_URL="https://openrouter.ai/api/v1"
export EVA_MODEL_NAME="nvidia/nemotron-3.5-lightning:free"
export EVA_API_KEY="your-openrouter-api-key"
python3 eva.py
```

Provider model IDs can change. If EVA says that it cannot find the configured model, choose an exact ID from the model list printed by EVA and set `EVA_MODEL_NAME` again.

On Linux and macOS, the first normal EVA launch automatically creates the `~/.local/bin/eva` launcher and adds `~/.local/bin` to `PATH` in `~/.bashrc` (Linux) or `~/.zshrc` (macOS). Run the command shown after the first launch:

```bash
source ~/.bashrc  # Linux
source ~/.zshrc   # macOS
```

You can then start an interactive session with:

```bash
eva
```

On Windows, run EVA with `python eva.py`.

## Configuration

| Variable | Default | Purpose |
| --- | --- | --- |
| `EVA_API_KEY` | none | Required bearer token sent to the configured endpoint. |
| `EVA_BASE_URL` | `https://api.deepseek.com/v1` | Base URL used for the `/models` and `/chat/completions` requests. |
| `EVA_MODEL_NAME` | `deepseek-v4-flash` | Model ID that must appear in the endpoint's `/models` response. |
| `EVA_HOME` | `<directory containing eva.py>/.eva` | Storage location for EVA's persistent instructions and saved sessions. |
| `EVA_SHELL` | `zsh` on macOS | Overrides the shell EVA uses on macOS. Linux uses `bash`; Windows uses PowerShell. |

Do not commit API keys to this repository, your shell configuration, or an `EVA.md` file.

## How EVA stores state

EVA uses two kinds of state:

- **Shared EVA state:** `EVA_HOME` contains `EVA.md` and a `sessions/` directory. By default, this is the `.eva/` directory next to `eva.py`.
- **Working-directory state:** EVA creates `.eva/hints.md` in the directory from which you launch it. This file holds memory hints for that particular project directory.

Sessions are named from the working-directory path, so starting EVA from a different directory starts a different session. The `-c` option clears the session for the current directory only.

`EVA.md` is optional. Add your durable instructions, project conventions, or reusable knowledge there; EVA includes its contents in the system prompt when it starts.

## Usage

### Interactive mode

```bash
python3 eva.py
```

Enter a request at the `[-] You:` prompt. EVA may call `run_cli` to inspect or act on the current working directory. By default, it uses a second model request to review commands; commands that are not classified as read-only require approval before execution.

Press `Ctrl+C` to interrupt EVA, a model response, or a running tool call. Interactive sessions are saved when interrupted.

### Run one request

```bash
python3 eva.py -u "Summarize the files in the current directory"
```

This runs one request and does not load or save the directory session by default. To load and save it, add `-s`:

```bash
python3 eva.py -s -u "Continue the previous task and summarize the current status"
```

### Goal mode

Goal mode repeatedly asks EVA to continue until the model outputs its internal completion marker. Use it only with a specific one-off request:

```bash
python3 eva.py -g -u "Create and verify a small hello-world Python script"
```

### Command approval modes

```bash
# Default: only commands classified as read-only run without an approval prompt
python3 eva.py

# Run every command without an approval prompt — use carefully
python3 eva.py --allow-all
```

### Session commands

```bash
# List all saved sessions
python3 eva.py --list-session

# Delete the session for the current directory
python3 eva.py --clear-session
```

## Command-line reference

| Option | Meaning |
| --- | --- |
| `-a`, `--allow-all` | Run all model-requested shell commands without the normal approval prompt. |
| `-l`, `--list-session` | List saved sessions. |
| `-c`, `--clear-session` | Clear the saved session for the current working directory. |
| `-u TEXT`, `--user-ask TEXT` | Run one request supplied on the command line. |
| `-s`, `--with-session` | With `-u`, load and save the current directory session. |
| `-g`, `--goal` | With `-u`, continue until EVA declares the task complete. |

## Troubleshooting

### "EVA_API_KEY environment variable is not set"

Set `EVA_API_KEY` in the same terminal session before launching EVA. EVA exits immediately if this variable is missing.

### EVA cannot connect to the API endpoint

Check `EVA_BASE_URL`, network access, and whether the endpoint exposes `/models`. EVA constructs the request URL by appending `/models` to `EVA_BASE_URL`; do not add an extra trailing slash.

### EVA reports that the configured model cannot be found

Set `EVA_MODEL_NAME` to an exact model ID returned by `GET <EVA_BASE_URL>/models`. EVA prints the IDs it received when the configured name is absent.

### API key is invalid or unauthorized

Confirm that the provider accepts the key as a bearer token and that the key can access the configured model. EVA reports HTTP 401 separately from other model-list failures.

### EVA says another instance is already running

EVA uses a lock file for the current directory's session. First make sure no other EVA process is still running for that directory. If it is not, remove the stale `.lock` file from the `sessions/` directory.

### The `eva` command is not found on Linux or macOS

Run the `source ~/.bashrc` or `source ~/.zshrc` command shown after the first launch, or open a new terminal. Confirm that `~/.local/bin` is on `PATH`.

## Verifying the repository

From the repository root, run the existing test suite and a syntax check:

```bash
python3 -m unittest discover -s tests -v
python3 -m py_compile eva.py
```

## Contributing

Contributions are welcome. Please see [CONTRIBUTING.md](./CONTRIBUTING.md) for the project's design principles and contribution ideas.
