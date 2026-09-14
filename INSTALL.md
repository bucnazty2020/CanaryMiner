# Installing and running CanaryMiner 1.0.0-rc.1

This guide applies to `CanaryMiner-1.0.0-rc.1-linux-x86_64.tar.gz`. For the normal first-installation path, use the [README Quick Start](README.md#quick-start--first-installation). This guide provides the same commands with additional configuration, state, and troubleshooting details.

> **Early-testing release.** The tested environment is Ubuntu 24.04 under WSL2 with an RTX 4070 and RTX 5080. A compatible Linux x86-64 system, NVIDIA driver, supported CUDA-capable GPU, and working network are required. Minimum driver versions, native-Linux portability, and the RTX 5070 with the exact archive are not established by this RC. Read [KNOWN_LIMITATIONS.md](KNOWN_LIMITATIONS.md), [LICENSE](LICENSE), and the archive's `THIRD_PARTY_NOTICES` before mining.

## Download and verify

Run these blocks in order in the same **Bash terminal on the mining machine**. Over SSH, run them in the server terminal, not your local Windows terminal. No `sudo` is used; the examples assume `curl`, `tar`, `sha256sum`, and `nano` are already available.

Use the release's **binary archive and `.sha256` file**, not GitHub's automatically generated “Source code” downloads. Start with a new version-specific application directory:

```bash
BASE="https://github.com/bucnazty2020/CanaryMiner/releases/download/v1.0.0-rc.1"
ARCHIVE="CanaryMiner-1.0.0-rc.1-linux-x86_64.tar.gz"

mkdir "$HOME/canaryminer-1.0.0-rc.1" &&
cd "$HOME/canaryminer-1.0.0-rc.1" &&
curl -fL --retry 3 -O "$BASE/$ARCHIVE" &&
curl -fL --retry 3 -O "$BASE/$ARCHIVE.sha256" &&
sha256sum -c "$ARCHIVE.sha256" &&
tar -xzf "$ARCHIVE" &&
cd CanaryMiner-v1.0.0-rc.1-linux-x86_64 &&
sha256sum --quiet -c SHA256SUMS &&
./app/canaryminer --version
```

The commands stop at the first failure. The outer checksum prints `OK` on success; `--quiet` suppresses successful internal-file lines, not failures. The binary is not invoked until both checksum checks succeed. To display every internal result instead, run `sha256sum -c SHA256SUMS` from the extracted package directory.

Published archive SHA-256:

```text
5a930b07f442ea9c6896ae633062e14f58daf8d996f8fb7d5a0919b83c1ff5b2
```

An existing `$HOME/canaryminer-1.0.0-rc.1` directory causes the download block to stop. Do not overwrite an existing installation or remove its state to get past this. After a successful installation, use the Start command below instead of repeating the download/setup sequence.

Keep `app/canaryminer` and its companion files together. Do not put configuration, state, or logs inside the extracted application directory. The paths below are example locations, not mandatory directory names:

| Purpose | Location used in this guide |
| --- | --- |
| Release files | `$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/` |
| Configuration | `$HOME/.config/canaryminer/config.json` |
| Persistent state | `$HOME/.local/share/canaryminer/rig-1` |

For an existing deployment, preserve its actual original state path rather than moving it to match these examples.

## Configuration details

From the extracted package directory, list the GPUs, create the configuration/state parents, and copy the template without overwriting an existing configuration:

```bash
nvidia-smi --query-gpu=index,uuid,name --format=csv,noheader &&
mkdir -p "$HOME/.config/canaryminer" "$HOME/.local/share/canaryminer" &&
cp -n ./config.example.json "$HOME/.config/canaryminer/config.json" &&
nano "$HOME/.config/canaryminer/config.json"
```

An existing config is not replaced by `cp -n`. If the copy is skipped or the editor does not open, inspect the existing configuration rather than overwriting it. Use another installed text editor if `nano` is unavailable.

The full template is:

```json
{
  "schema": "canary-config-v1",
  "wallet": "REPLACE_WITH_YOUR_PUBLIC_PRL1_PAYOUT_ADDRESS",
  "worker": "rig_1",
  "pools": ["kryptex"],
  "devices": [{"index": 0}]
}
```

Replace the wallet placeholder with your **public mainnet Pearl payout address**. It intentionally fails validation until replaced. Never enter seed phrases, private keys, or exchange credentials.

Use one ASCII worker name of 1–64 characters for the rig. Set `pools` to `["kryptex"]` or `["herominers"]`. An ordered list requests that explicit order; it does not establish failover qualification. Custom URLs, tuning keys, fee overrides, account-based auto-exchange, and unsupported keys are not accepted by this configuration interface.

### Select GPUs

The template selects **GPU 0 only**. The first command above lists device identities before opening the editor. To select GPUs 0 and 1, replace the template's `devices` section with:

```json
"devices": [
  {"index": 0},
  {"index": 1}
]
```

Select each intended physical GPU once, by either its NVML index or full physical GPU UUID. Do not combine selectors in one device entry or select the same physical device twice. Consult `./app/canaryminer --help` before using options not shown here; do not guess unsupported configuration fields.

In Nano, save with Ctrl+O, press Enter, then exit with Ctrl+X. Validate before setup:

```bash
./app/canaryminer check-config "$HOME/.config/canaryminer/config.json" --json
```

For interface details without starting mining:

```bash
./app/canaryminer --help
./app/canaryminer setup --help
```

## Initialize a new allocation once

**Only use this section for a genuinely new allocation.** An existing deployment must keep its original mining-state ledger and path.

This RC requires you to reserve a **fleet slot from 0 through 65535**. Record it across your entire CanaryMiner fleet, including development tests, and never reuse it. `0` is suitable only for your first-ever allocation across all your machines. Otherwise, consult your fleet records for a never-used slot. Automatic cross-machine allocation is not part of this documented interface; a new folder does not make an old slot unused.

This block validates the configuration and prompts for the slot, then initializes state. It does not start mining:

```bash
./app/canaryminer check-config "$HOME/.config/canaryminer/config.json" --json &&
read -r -p "Unused fleet slot (0-65535): " FLEET_SLOT &&
./app/canaryminer setup \
  --state "$HOME/.local/share/canaryminer/rig-1" \
  --config "$HOME/.config/canaryminer/config.json" \
  --new-fleet-slot "$FLEET_SLOT"
```

The `rig-1` state directory must not already exist; Setup refuses to overwrite it. Creating only its parent directory is intentional. If Setup reports an error, stop and inspect the error before starting or changing anything.

## Start, stop, and restart

After successful setup, stop other miners using the selected GPUs through their normal controls. Start CanaryMiner in the foreground:

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" start \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

The monitoring display opens with Start. Mining continues until stopped; development smoke-test durations are not runtime limits. `--no-watch` suppresses the display while keeping foreground ownership.

For a first SSH test, keep the Start session open. This guide does not install a background service or promise behavior after an SSH disconnect. Do not open a second owner of the same state to work around a disconnected session.

### Stop and confirm cleanup

**Ctrl+C in the Start terminal requests shutdown of the owned rig.** Wait for Start to exit, then run:

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" status \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

Check the terminal snapshot for stopped devices, verified cleanup, and no errors. A Stop request is not proof that shutdown has completed.

From a separate terminal, request shutdown with:

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" stop \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

Then wait for the original Start process to exit and check Status. Do not delete state or launch a second owner while waiting for shutdown.

### Watch from another terminal

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" watch \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

**Ctrl+C in a separate Watch terminal closes only that view, not the miner.** The examples use absolute executable and state paths so a new terminal does not depend on temporary shell variables. To match the original release's invocation environment, open the extracted package directory before running them.

### Restart later

Run the same Start command again:

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" start \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

**Do not repeat Setup.** Preserve and reuse the existing state at its original physical path.

## Existing deployments and state

Persistent state tracks finite-work ownership; it is not disposable cache. Copying state, restoring an old snapshot, resetting a ledger, reusing a fleet slot, or running two owners can repeat spent work and is unsupported. Do not move the state directory, delete it to resolve an error, or use a new slot merely to bypass a failed initialization.

For a normal restart, use Start with the original state path. For an existing development deployment, do not assume a new public-release folder creates a new allocation or implies state-format compatibility.

The existing import interface is for an **authentic prepared ledger reference identifying the same existing lease**, not for inventing replacement state:

```bash
./app/canaryminer setup \
  --state "$HOME/.local/share/canaryminer/rig-import" \
  --config "$HOME/.config/canaryminer/config.json" \
  --lease /absolute/path/ledger-reference.json
```

This is an advanced example, not a first-run step. Do not invent reference fields, use a receipt from spent work, copy a live ledger into another miner, or keep an old owner running. Import and recovery need a valid prepared reference and an understood ownership transition; they are not reset shortcuts.

## Troubleshooting the first run

| Symptom | What to do |
| --- | --- |
| Download fails | Stop. Check the exact release URL and network connection; do not continue with a partial archive. |
| Any checksum fails | Do not extract or execute unverified content. A failed internal check also means do not run the executable. |
| Install directory already exists | Inspect the existing installation. For a completed setup, use Start, not another initialization. |
| `nano` is unavailable | Edit the copied config with another installed text editor; keep it valid JSON. |
| Wallet/config validation fails | Use a public mainnet PRL payout address, supported keys, valid JSON, and real GPU selectors. Never substitute private credentials. |
| Setup says state exists, or slot history is uncertain | Preserve the state and consult your deployment/fleet records. Do not guess, reset, or overwrite. |
| GPU/runtime/driver error | Record the exact error, GPU, driver, and OS. Native-Linux portability and minimum driver versions remain unqualified; do not assume `sudo` or a state reset is the fix. |
| Stop returns but GPUs still show activity | Wait for the original Start process to exit, then inspect Status for stopped devices and cleanup. Do not start a second owner. |

For issue reports, include the version, OS, GPU/driver details, pool profile, and relevant error output with personal information removed. Never post seed phrases, private keys, exchange credentials, or state-ledger copies.

Local TH/s, pool estimates, accepted replies, credited work, and earnings are different measures. No performance, uptime, pool credit, reward, or income is promised. Repository documentation changes do not replace the published archive or add a setup wizard to the RC binary.
