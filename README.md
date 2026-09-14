# CanaryMiner — Pearl GPU miner for Linux

CanaryMiner is closed-source Pearl (PRL) mining software distributed as a Linux x86-64 binary for NVIDIA Ada and Blackwell GPUs.

**CanaryMiner 1.0.0-rc.1 is a public prerelease.** The exact compiled application passed integrity and admission checks plus a bounded 150-second dual-GPU Start and Watch smoke on Ubuntu 24.04 under WSL2 with an RTX 4070 and RTX 5080. Three user proofs were independently verified, transmitted, and acknowledged by the pool; the run recorded no reject, stale, pending, unknown, malformed, or unmatched outcome. Pool credit and pool mode were not independently verified.

Implementation source and private build history are maintained separately. This repository distributes the executable archive, checksums, instructions, license, notices, and issue tracking.

## Scope

- Linux x86-64.
- NVIDIA Ada SM 89 and Blackwell SM 120 profiles.
- One worker name per rig, with every selected GPU visible separately in the local display.
- Named Kryptex and HeroMiners pool profiles.
- Persistent Setup, Start, Stop, Status, and read-only Watch controls.
- A disclosed 1% developer fee based on productive device-mining time.

Native Windows, HiveOS, LuckyPool, other GPU architectures, solo mining, custom pool endpoints, TLS, account-based auto-exchange, and PRL/NOCK merge mining are outside this RC.

## Pool profiles

- **Kryptex:** prl-us.kryptex.network:7048 over TCP.
- **HeroMiners:** us2.pearl.herominers.com:1200 over TCP.

Pool service and protocol behavior can change. Compatibility does not promise credited work, yield, or income.

## Developer fee

CanaryMiner targets a 99:1 allocation of productive GPU mining time between the user's public Pearl address and this developer address:

prl1pr2530rjgquj97eff32j5pzlfw4qzkasl0t0acdx4hc5k498j770qjmzcuz

Startup and idle time are excluded from that accounting basis. The percentage does not describe wall-clock uptime, accepted shares, rewards, or income. The short RC smoke did not reach a developer work window, so repeated fee cycles and interruption behavior remain unqualified. See LICENSE.

## Reporting

Local TH/s means one trillion completed multiply-accumulate operations per second, including user and developer work. It is not pool-accepted hashrate. Each open view estimates per-GPU 1-hour, 6-hour, and 24-hour rolling local rates. An asterisk marks incomplete warmup. Closing the view discards that view history.

Accepted counts start at the current view's first observed baseline. Core clock, memory clock, driver-reported GPU watts, fan speed, and temperature are shown when fresh telemetry is available. GPU watts are not whole-system wall power.

## Install

Download CanaryMiner-1.0.0-rc.1-linux-x86_64.tar.gz and its .sha256 file from the GitHub prerelease. Follow [INSTALL.md](INSTALL.md), and read [KNOWN_LIMITATIONS.md](KNOWN_LIMITATIONS.md), [LICENSE](LICENSE), and THIRD_PARTY_NOTICES before mining.

No performance, uptime, reward, profitability, or income is promised.
