# CanaryMiner 1.0.0-rc.1 Linux — known limitations

**This is a prerelease with bounded qualification. Read these limitations before mining.**

## Qualification

The exact compiled application completed integrity and runtime-admission checks and a 150-second Start and Watch smoke on Ubuntu 24.04 under WSL2 with one RTX 4070 and one RTX 5080. Both devices stopped cleanly, cleanup was verified, and the controller recorded no errors.

The run produced three user candidates. All three passed independent official verification, were transmitted, and received positive pool replies. Reject, stale, retired, pending, unknown, malformed, and unmatched outcomes were zero. This does not establish pool-side credit, pool mode, long-term acceptance, yield, recovery, portability, or endurance.

Native Linux outside WSL2, RTX 5070 against the exact public archive, every model in an architecture family, minimum driver versions, and every distribution remain unqualified.

## Excluded scope

Native Windows, HiveOS, LuckyPool, custom pools, other GPU architectures, solo mining, TLS, account-based auto-exchange, and PRL/NOCK merge mining are excluded from this RC.

Kryptex and HeroMiners are the only named pool profiles. Pool behavior can change, and an accepted protocol reply is not proof of credited work or earnings.

## Developer fee

The software targets a 99:1 allocation of productive GPU mining time. The short exact RC smoke did not reach a developer work window: developer routes were authorized and received jobs but performed no attempts or submissions. Repeated fee periods, restarts, interruptions, timing overruns, and developer-route outages have not completed qualification. The fee is not a promise of an exact share of uptime, accepted shares, rewards, or income.

## State and controls

Setup establishes durable finite-work ownership. Fleet slots must be unique. Copying state, restoring an old snapshot, resetting a ledger, or running two owners can repeat spent coordinates and is unsupported.

Start, Stop, Status, and Watch are implemented. Ctrl+C in Start requests rig shutdown; Ctrl+C in a separate Watch closes only the view. Stop records a request and can return before shutdown is confirmed. Inspect terminal Status after the Start process exits.

## Metrics

Local TH/s is completed multiply-accumulate work, not pool-accepted hashrate. The 1-hour, 6-hour, and 24-hour rates exist only in the current view and reset when it closes or observations become discontinuous. Accepted counts also start from the view's first observed baseline. Unsupported or stale clock, memory clock, power, fan, and temperature telemetry remains unavailable. Driver-reported GPU power is not wall power.

No mining rate, uptime, acceptance rate, pool credit, reward, profitability, or income is promised.

## Distribution

The proprietary binary is governed by LICENSE. Third-party material remains governed by THIRD_PARTY_NOTICES. Notice coverage uses a conservative exact-file mapping, but this is not legal advice. Build reproducibility, third-party wheel-to-source linkage, and independent legal review are not established. Private implementation source and development evidence are not included.
