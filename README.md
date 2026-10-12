# Oshea

**Semi-automatic binary analysis toolkit for CTF Pwn and Reverse Engineering.**

`ctftool` is a command-line tool that helps CTF players analyze Linux ELF binaries, identify security protections, discover useful ROP gadgets, inspect suspicious functions, and build an actionable analysis plan.

It automates repetitive binary triage tasks and generates exploit scaffolding to help you focus on understanding and solving the challenge.

> **No AI. No internet required for core analysis.**  
> `ctftool` combines existing reverse engineering and binary analysis tools into one workflow.

## Features

- **Binary Protections** — identify PIE, NX, Stack Canary, RELRO, Fortify, static linking, and stripped symbols.
- **Function Analysis** — identify potentially dangerous functions and possible win or backdoor functions.
- **ROP Gadget Discovery** — search for useful gadgets such as `pop rdi; ret`, `leave; ret`, and syscall instructions.
- **String Analysis** — locate interesting strings, including `/bin/sh`, and inspect their addresses.
- **Reverse Engineering Hints** — detect possible packers, anti-debugging indicators, high entropy, and interesting comparison constants.
- **String Cross-References** — map strings to code locations, including in stripped binaries.
- **Static Binary Analysis** — inspect syscall locations, writable memory regions, and useful write gadgets.
- **Heap Analysis** — identify allocation and deallocation sites and potential UAF or double-free indicators.
- **ROP Chain Generation** — attempt to construct a `system` or `execve` ROP chain.
- **Symbolic Execution** — optionally use `angr` to search for inputs that reach a target address or produce a success string.
- **Exploit Scaffolding** — generate a starter exploit script using `pwntools`.
- **JSON Output** — export analysis findings for further processing.

## Requirements

- Linux (Kali Linux, Ubuntu, Debian, or a similar distribution)
- Python 3.9 or newer
- Python dependencies listed in `pyproject.toml`

Optional tools and dependencies:

- `gdb` — useful for debugging and exploit offset discovery.
- `angr` — required for symbolic execution features.
- A challenge-specific `libc.so.6` — useful for additional libc and one-gadget analysis.

Some features depend on external tools being installed and available in your `PATH`.

## Installation

### 1. Clone the repository

Replace `YOUR-USERNAME` with your GitHub username.

```bash
git clone https://github.com/YOUR-USERNAME/ctftool.git
cd ctftool
```

### 2. Create a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

If your system does not have `venv` installed, install the appropriate Python virtual environment package for your Linux distribution.

### 3. Install ctftool

```bash
python -m pip install --upgrade pip
python -m pip install -e .
```

Verify the installation:

```bash
ctftool --help
```

### 4. Enable symbolic execution (optional)

The `angr` dependency is optional because it is significantly heavier than the core dependencies.

```bash
python -m pip install -e ".[solve]"
```

## Usage

### Basic binary analysis

```bash
ctftool ./challenge
```

This prints an analysis report with binary protections, interesting functions, ROP gadgets, reverse engineering hints, and a prioritized analysis plan.

### Generate an exploit skeleton

```bash
ctftool ./challenge --skeleton exploit.py
```

Generate a JSON report as well:

```bash
ctftool ./challenge --skeleton exploit.py --json report.json
```

The generated exploit is a starting point, not a guaranteed working exploit. Review and adapt it to the challenge.

### Disassemble a function

```bash
ctftool ./challenge --disasm main
```

### Analyze a provided libc

```bash
ctftool ./challenge --libc ./libc.so.6
```

### Analyze heap usage

```bash
ctftool ./challenge --heap
```

### Generate a ROP chain

```bash
ctftool ./challenge --ropchain --libc ./libc.so.6
```

The tool attempts to construct a suitable chain based on the available binary information. The result may require manual adjustments.

### Find string cross-references

List interesting string references:

```bash
ctftool ./challenge --xref
```

Filter by a string:

```bash
ctftool ./challenge --xref "malformed"
```

Include additional library and toolchain strings:

```bash
ctftool ./challenge --xref --xref-all
```

### Analyze a statically linked binary

```bash
ctftool ./challenge --static
```

### Use symbolic execution

Find an input that makes the program print `Correct` while avoiding paths that print `Wrong`:

```bash
ctftool ./crackme --solve-find Correct --solve-avoid Wrong
```

Search for an input that reaches a specific address:

```bash
ctftool ./crackme --solve-addr 0x401337
```

Generate a standalone solver script:

```bash
ctftool ./crackme --solve-find Correct --solve-script solve.py
```

Symbolic execution can take significant time and memory, depending on the binary and its execution paths.

### View all options

```bash
ctftool --help
```

## Example Workflow

A typical CTF workflow might look like this:

1. Run `ctftool` against the challenge binary.
2. Review the security protections and interesting functions.
3. Examine the suggested ROP gadgets and strings.
4. Follow the prioritized analysis plan.
5. Generate an exploit skeleton if useful.
6. Debug and adapt the solution using tools such as GDB and `pwntools`.

The tool assists with triage and preparation. It does not replace manual analysis or guarantee that a vulnerability can be exploited.

## Project Structure

```text
ctftool/
├── ctftool/
│   ├── __init__.py
│   ├── __main__.py
│   ├── triage.py
│   ├── pwn_mod.py
│   ├── rev_mod.py
│   ├── heap_mod.py
│   ├── static_mod.py
│   ├── ropchain.py
│   ├── solve_mod.py
│   ├── plan.py
│   ├── skeleton.py
│   └── ui.py
├── samples/
├── README.md
├── LICENSE
├── pyproject.toml
└── install.sh
```

The `samples/` directory contains example binaries and source files for experimentation.

## Limitations

- Analysis results are heuristic and may include false positives or miss vulnerabilities.
- Some features require optional dependencies or external tools.
- Automatically generated ROP chains and exploit skeletons may need manual changes.
- Symbolic execution may not finish on complex programs.
- The tool currently targets Linux ELF binaries.

## Contributing

Contributions, bug reports, feature requests, and improvements are welcome.

If you find a bug or have an idea for improvement, open an issue or submit a pull request.

When reporting a problem, include:

- Your operating system and Python version.
- The command you executed.
- The relevant error message.
- A minimal reproducible example, if possible.

Please avoid uploading confidential binaries or sensitive challenge data.

## Responsible Use

`ctftool` is intended for cybersecurity education, authorized security research, reverse engineering, and CTF competitions.

Use it only on binaries you own, are authorized to analyze, or are permitted to test under the applicable rules.

You are responsible for ensuring that your use of this tool complies with applicable laws and competition policies.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
