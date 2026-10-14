# Linux Diagpack

A ***stupid-simple*** wrapper script for creating a compressed diagnostic bundle from GNU/Linux systems.
It gathers system and hardware inventories, selected logs, filesystem metadata, and optional `sos` and `hw-probe` output.

## Features

- Creates a `.tar.zst` diagnostic archive in the user's home directory.
- Collects selected system information, logs, and filesystem/path metadata.
- Uses `sos report` or `sosreport` when available.
- Forwards options to sos with `--sos-args`.
- Optionally runs an extended hardware check or uploads a saved hardware-probe package.
- Does not install packages.
- Removes its temporary `work` directory before creating the archive.

## Requirements

GNU/Linux and GNU tar. The script explicitly checks for these commands:

`tar`, `zstd`, `python3`, `find`, `stat`, `cp`, `sha256sum`, `sort`, `xargs`, and `getent`.

It also uses standard system utilities available on most GNU/Linux distributions. Optional collections may use tools such as `sos`, `hw-probe`, or `journalctl`.

## Usage

```text
Usage: sudo -E diagpack [options]

Options:
  --extended-hw-check   Run hw-probe's -check-extended option (if supported)
  --upload-hw-probe     Upload the saved hw-probe package with -upload -src
  --sos-args ARGS...    Pass remaining arguments to sos report or sosreport.
                        Must be last; example: --sos-args --all-logs
  -h, --help            Show this help

By default, collection is local. --upload-hw-probe explicitly uploads the
saved hw-probe package. No packages are installed.

Output is written to the user's home directory. /home and /root are excluded
from the filesystem index and raw-log copy.

Core-dump contents are not collected. Crash metadata and kernel debug-symbol
availability are recorded, but core dumps are not analyzed and symbols are
not loaded. The script does not run tracing or stress-test workloads.
This is a unified diagnostic collector, NOT A DEBUGGER.
```

## Compatibility note

Archive creation preserves SELinux labels where supported by GNU tar. Running the script under enforcing SELinux or AppArmor policies has not been specifically tested;
those policies may restrict collection or prevent optional tools from running. The script does not change security-policy settings.
