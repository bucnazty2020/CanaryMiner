# CanaryMiner — Pearl GPU miner for Linux

CanaryMiner is closed-source **Pearl (PRL) mining software** for Linux x86-64, targeting **NVIDIA Ada (SM89) and Blackwell (SM120)** GPUs. It includes Kryptex and HeroMiners pool profiles, per-GPU monitoring, and a disclosed **1% developer-fee target based on productive GPU mining time**.

> **v1.0.0-rc.1 is an early-testing release, not a stable release.** The exact build was tested on Ubuntu 24.04 under WSL2 with an RTX 4070 and RTX 5080. Native Linux, the RTX 5070 with this archive, pool-side credit/mode, and sustained developer-fee operation remain unqualified. Read [KNOWN_LIMITATIONS.md](KNOWN_LIMITATIONS.md) before mining.

## Quick start — first installation

**Download and verify → enter your settings → initialize once → start.** After the initial setup, only the Start command is needed.

Use the following blocks in order in the **same Bash terminal on the Linux machine that will mine**. An SSH terminal is fine. You need a compatible NVIDIA driver, `curl`, `tar`, `sha256sum`, and a text editor; the examples use `nano`. These steps do not use `sudo` or install a driver.

**Already used CanaryMiner, including a development build?** Preserve your existing configuration and mining state. Do not treat reinstalling as a new allocation. See [existing deployments](INSTALL.md#existing-deployments-and-state).

### 1. Download and verify

Download the **binary archive**, not GitHub's automatically generated “Source code” archives. This block creates a new version-specific directory in your home folder, verifies the package, and prints the version. **It does not start mining.**

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

The outer checksum should report `OK`; the internal check is quiet unless there is a problem. The version command runs only after all checks succeed. **Stop on any error.** An existing installation directory deliberately stops this block rather than being overwritten.

### 2. Enter your wallet, worker, and GPUs

List the GPUs, copy the example to a separate configuration directory, then edit it:

```bash
nvidia-smi --query-gpu=index,uuid,name --format=csv,noheader &&
mkdir -p "$HOME/.config/canaryminer" "$HOME/.local/share/canaryminer" &&
cp -n ./config.example.json "$HOME/.config/canaryminer/config.json" &&
nano "$HOME/.config/canaryminer/config.json"
```

Replace `REPLACE_WITH_YOUR_PUBLIC_PRL1_PAYOUT_ADDRESS` with your **public mainnet PRL payout address**. Never enter a seed phrase, private key, or exchange credentials. Choose a worker name for this rig. The template uses **Kryptex and GPU 0 only**; change `kryptex` to `herominers` to select that profile.

The first command lists GPU identities before opening the editor. To use GPUs 0 and 1, replace the `devices` section with:

```json
"devices": [
  {"index": 0},
  {"index": 1}
]
```

Save in Nano with **Ctrl+O**, press **Enter**, then exit with **Ctrl+X**. The full configuration example and UUID selection rules are in [INSTALL.md](INSTALL.md#configuration-details). Read [LICENSE](LICENSE) and the archive's `THIRD_PARTY_NOTICES` before use.

### 3. Initialize once

This RC requires an **unused fleet slot**, a number from **0 through 65535** reserved for a new mining-state allocation. Record it and never reuse it across your CanaryMiner fleet, including development tests. **Use `0` only when this is your first-ever allocation across all your machines.** Otherwise, use a number your fleet records confirm has never been allocated; do not guess.

The following block validates your configuration and prompts for the slot. It creates persistent state, but **does not start mining**:

```bash
./app/canaryminer check-config "$HOME/.config/canaryminer/config.json" --json &&
read -r -p "Unused fleet slot (0-65535): " FLEET_SLOT &&
./app/canaryminer setup \
  --state "$HOME/.local/share/canaryminer/rig-1" \
  --config "$HOME/.config/canaryminer/config.json" \
  --new-fleet-slot "$FLEET_SLOT"
```

If setup fails or says state already exists, **do not delete/reset state or rerun setup with a different slot to bypass the error**. See [state guidance](INSTALL.md#existing-deployments-and-state).

### 4. Start mining

After successful setup, stop any other miner on the selected GPUs using its normal shutdown method. Then run:

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" start \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

This starts foreground mining with the local display. Keep the SSH terminal open during your first test. **For every later launch, reuse this same Start command and state directory. Do not repeat initialization.**

To stop, press **Ctrl+C in the Start terminal**. After Start exits, check that Status confirms stopped devices, verified cleanup, and no errors:

```bash
"$HOME/canaryminer-1.0.0-rc.1/CanaryMiner-v1.0.0-rc.1-linux-x86_64/app/canaryminer" status \
  --state "$HOME/.local/share/canaryminer/rig-1"
```

For a separate Watch view, remote Stop, detailed configuration, or troubleshooting, see [INSTALL.md](INSTALL.md).

## What is included

- Linux x86-64 binary with Ada SM89 and Blackwell SM120 profiles.
- Kryptex and HeroMiners pool profiles; one worker name per rig, with selected GPUs shown separately.
- Persistent Setup, Start, Stop, Status, and read-only Watch controls.

Native Windows, HiveOS, LuckyPool, other GPU architectures, solo mining, custom pool endpoints, TLS, account-based auto-exchange, and PRL/NOCK merge mining are outside this RC. An interactive setup wizard and automatic fleet-slot allocation are **not features of this release**.

## Release qualification

The exact compiled application passed integrity and admission checks and a bounded **150-second dual-GPU Start and Watch test** under WSL2 Ubuntu 24.04 with an RTX 4070 and RTX 5080. All three user proofs were independently verified, transmitted, and acknowledged by the pool, with no reject, stale, pending, unknown, malformed, or unmatched outcomes recorded.

These are bounded test results, not proof of pool-side credit, pool mode, long-term acceptance, portability, recovery, or endurance. The short test did not reach a developer mining window. See [KNOWN_LIMITATIONS.md](KNOWN_LIMITATIONS.md).

## Pool profiles

| Profile | TCP endpoint |
| --- | --- |
| `kryptex` | `prl-us.kryptex.network:7048` |
| `herominers` | `us2.pearl.herominers.com:1200` |

Pool behavior can change. Compatibility and positive protocol replies do not promise credited work, yield, or income.

## Developer fee

CanaryMiner targets a **99:1 allocation of productive GPU mining time** between the user's public Pearl address and this developer address:

```text
prl1pr2530rjgquj97eff32j5pzlfw4qzkasl0t0acdx4hc5k498j770qjmzcuz
```

Startup and idle time are excluded. The percentage does not describe wall-clock uptime, accepted shares, rewards, or income. Repeated fee cycles and interruption behavior remain unqualified. See [LICENSE](LICENSE).

## Understanding the display

Local TH/s means one trillion completed multiply-accumulate operations per second, including user and developer work. **It is not pool-accepted hashrate.** Each open view estimates per-GPU 1-hour, 6-hour, and 24-hour rolling local rates; an asterisk marks incomplete warmup. Closing a view discards its history.

Accepted counts start at the current view's first observed baseline. Core clock, memory clock, driver-reported GPU watts, fan speed, and temperature appear when fresh telemetry is available. GPU watts are not whole-system wall power.

## Distribution and support

Implementation source and private build history are maintained separately. This repository distributes release assets, checksums, instructions, license information, and issue tracking. **Updating these repository instructions does not change the published RC archive or add features to its executable.**

For issue reports, include the CanaryMiner version, GPU model, driver version, operating system, selected pool profile, and relevant error output. Remove personal information and credentials; do not post private keys, seed phrases, or copies of mining-state ledgers.

No performance, uptime, reward, profitability, or income is promised.
