# CanaryMiner 1.0.0-rc.1 Linux installation and use

This guide applies only to CanaryMiner-1.0.0-rc.1-linux-x86_64.tar.gz.

The tested environment is Ubuntu 24.04 under WSL2 with RTX 4070 and RTX 5080. A compatible x86-64 Linux system, NVIDIA driver, CUDA-capable supported GPU, and working network connection are required. A minimum driver version and native-Linux portability are not established by this RC.

## Verify and extract

Download the archive and checksum from the GitHub prerelease into the same directory:

~~~sh
sha256sum -c CanaryMiner-1.0.0-rc.1-linux-x86_64.tar.gz.sha256
tar -xzf CanaryMiner-1.0.0-rc.1-linux-x86_64.tar.gz
cd CanaryMiner-v1.0.0-rc.1-linux-x86_64
sha256sum -c SHA256SUMS
~~~

Every internal checksum must report OK. Keep canaryminer and all companion files together. Do not put configuration, state, or logs inside the application directory.

Inspect the interface without mining:

~~~sh
./app/canaryminer --version
./app/canaryminer --help
./app/canaryminer setup --help
~~~

## Configure

Create separate persistent configuration and state parents:

~~~sh
mkdir -p "$HOME/.config/canaryminer" "$HOME/.local/share/canaryminer"
cp -n ./config.example.json "$HOME/.config/canaryminer/config.json"
~~~

Edit the copied config:

- Replace REPLACE_WITH_YOUR_PUBLIC_PRL1_PAYOUT_ADDRESS with your public mainnet Pearl payout address. The placeholder intentionally fails validation.
- Set one 1–64 character ASCII worker name for the rig.
- Choose kryptex or herominers. The template selects Kryptex. An ordered list requests that explicit order; it does not prove failover qualification.
- Select each intended GPU once by either NVML index or full physical GPU UUID. Do not use both selectors in one device entry.
- Do not add tuning, fee overrides, custom URLs, seed phrases, private keys, exchange credentials, or unsupported keys.

Inspect device identities:

~~~sh
nvidia-smi --query-gpu=index,uuid,name --format=csv,noheader
~~~

Validate the config:

~~~sh
./app/canaryminer check-config "$HOME/.config/canaryminer/config.json" --json
~~~

## Initialize state once

For a genuinely new allocation, choose and record an unused fleet slot from 0 through 65535 across your whole fleet:

~~~sh
./app/canaryminer setup \
  --state "$HOME/.local/share/canaryminer/rig-1" \
  --config "$HOME/.config/canaryminer/config.json" \
  --new-fleet-slot YOUR_UNUSED_FLEET_SLOT
~~~

The state directory must not already exist. Setup stores durable finite-work ownership and refuses to overwrite state. Never reuse a fleet slot, reset state to repeat work, copy one state directory to another miner, restore an old snapshot, or run two owners of the same ledger. Keep the state at its original physical path.

Existing deployments must preserve their ledger. Import only an authentic prepared ledger reference that identifies the same existing lease:

~~~sh
./app/canaryminer setup \
  --state "$HOME/.local/share/canaryminer/rig-import" \
  --config "$HOME/.config/canaryminer/config.json" \
  --lease /absolute/path/ledger-reference.json
~~~

Do not invent reference fields or use a receipt from spent work.

## Start and observe

Start in the foreground:

~~~sh
./app/canaryminer start --state "$HOME/.local/share/canaryminer/rig-1"
~~~

Ctrl+C in this Start terminal requests a clean stop of the owned rig. Mining continues until stopped; development smoke durations are not runtime limits. Add --no-watch to suppress the display while keeping foreground ownership.

From another terminal:

~~~sh
./app/canaryminer status --state "$HOME/.local/share/canaryminer/rig-1"
./app/canaryminer watch --state "$HOME/.local/share/canaryminer/rig-1"
~~~

Ctrl+C in a separate Watch terminal closes only that view.

Request shutdown from another terminal:

~~~sh
./app/canaryminer stop --state "$HOME/.local/share/canaryminer/rig-1"
~~~

A successful Stop response records the request. Wait for the Start command to exit, then inspect Status for a closed terminal snapshot, stopped devices, verified cleanup, and no errors. Restart with the same Start command and persistent state; do not rerun Setup.

Read [KNOWN_LIMITATIONS.md](KNOWN_LIMITATIONS.md), [LICENSE](LICENSE), and THIRD_PARTY_NOTICES. Local TH/s, pool estimates, accepted replies, credited work, and earnings are different measures.
