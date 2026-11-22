# linux-zako

Patch queue for a Linux kernel built from [Linux-libre](https://www.linux-libre.fsfla.org/)
with the [Zen](https://github.com/zen-kernel/zen-kernel) patchset and the
[BORE](https://github.com/firelzrd/bore-scheduler) scheduler.

Patches only — no kernel tree, no build scripts, no releases.

## Baseline

| Field | Value |
| --- | --- |
| Series | 7.3 |
| Upstream | `linux-libre-7.3.5-gnu.tar.xz` |
| Source | https://www.linux-libre.fsfla.org/pub/linux-libre/releases/LATEST-7.3.N/ |
| SHA-256 | `00f30f9cc4328a4b0a8d8a71349f9dc9fabed762f0a27993160e319377ef5753` |

Linux-libre ships already deblobbed, so this queue contains no `deblob` step and
no firmware or microcode blobs of its own.

## Queue

Apply in the order listed in `series/7.3`.

| Patch | Upstream | Delta |
| --- | --- | --- |
| `0001-zen-7.3.5-zen1.patch` | zen-kernel `v7.3.5-zen1` | verbatim except one `Makefile` hunk: `EXTRAVERSION` stays `-gnu`, only `NAME` is taken |
| `0002-sched-bore-7.0.0.patch` | firelzrd/bore-scheduler `7.0.0` | rebased onto 0001: the `fair.c` base-slice branch and the `debug.c` debugfs entries are nested inside Zen's `CONFIG_ZEN_INTERACTIVE` / `CONFIG_SCHED_ALT` blocks |

## Building

```sh
tar xf linux-libre-7.3.5-gnu.tar.xz
cd linux-libre-7.3.5
git apply ../patches/7.3/0001-zen-7.3.5-zen1.patch
git apply ../patches/7.3/0002-sched-bore-7.0.0.patch
make olddefconfig          # select CONFIG_SCHED_BORE
make LOCALVERSION=-zako1
```

`CONFIG_SCHED_ALT` (BMQ/PDS) is not compatible with what this queue does: it
drops cgroup v2 `cpu.weight`/`cpu.max`, `sched_ext` and `SCHED_CORE`. Leave it off.

## Versioning

- Branch `7.3/main`, one per kernel series.
- Tag `v7.3.5-zako1` — upstream version plus a revision counter for this queue.
- Tags are never moved or deleted; every published version stays.

## Update policy

The queue moves to a new upstream release only when all three inputs have caught
up: the Linux-libre point release, a Zen tag for that release, and a BORE patch
for that series. Until then the queue stays where it is.
