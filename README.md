# CanaryMiner

CanaryMiner is a Pearl-only miner being prepared for a v1.0 binary release for NVIDIA Ada and Blackwell GPUs.

**Release status: in preparation.** No qualified v1.0 download is available yet.

This repository is intended for executable releases, installation instructions, required third-party notices, checksums and issue tracking. CanaryMiner implementation source and private build history are maintained separately.

## Planned v1.0 release

- Linux x86-64 and native Windows x64 executables, subject to final artifact qualification.
- Requested pool integrations: Kryptex, HeroMiners and LuckyPool. Each will be listed as supported only after qualification.
- A disclosed 1% developer fee based on productive device mining time. It does not mean exactly 1% of accepted shares or earnings.
- User-controlled Start, Stop, Status and read-only monitoring with per-GPU information.
- Local Pearl TH/s, 1-hour / 6-hour / 24-hour averages, terminal-session share counts, core and memory clocks, GPU power, fan speed and temperature.

The authorized developer-fee payout address is:

`prl1pr2530rjgquj97eff32j5pzlfw4qzkasl0t0acdx4hc5k498j770qjmzcuz`

Users will configure their own mining payout addresses. No wallet seed phrase or private key is required.

A HiveOS adapter may be provided separately; it will be identified as experimental unless tested on HiveOS.

Qualified downloads will include exact supported-platform information, fee behavior, third-party licenses and SHA-256 checksums. Performance and compatibility claims will be limited to retained test evidence. Local Pearl work rates and pool estimates are distinct; GPU power is driver-reported, not wall power.
