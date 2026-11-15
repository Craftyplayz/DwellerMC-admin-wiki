# spark

| | |
|---|---|
| Version | The copy bundled with Paper 26.3 (no jar in `plugins/`) |
| Command | `/spark` |
| Permission | `spark` (all subcommands) or `spark.<subcommand>`. Op by default. |
| Data folder | `plugins/spark/` (`config.json`, `tmp/`) |

spark is a profiler. It shows where the server spends its time, how many ticks per second it manages, how much memory it uses and what the garbage collector is doing. Paper ships it built in. The console prints `This server bundles the spark profiler` at start.

## What it is for on DwellerMC

- **Chunk generation lag.** DwellerCore generates a 2048-block-tall world with caves, ores and structures. Walking into new areas can cost a lot of tick time. Run a profile while someone explores.
- **Mob levels and elites.** Check whether DwellerCore's spawn and level code appears near the top of the profile.
- **Memory.** The 2048-tall world loads taller chunks. Use `/spark health --memory` to watch the heap. CoreProtect's DuckDB also uses memory outside the Java heap (see [CoreProtect](coreprotect.md)).
- **WorldEdit and WorldGuard.** After a big edit, see whether the server recovered. See [WorldEdit](worldedit.md) and [WorldGuard](worldguard.md).

## Commands

All need `spark` or `spark.<name>`. Console can run all of them.

| Command | Does |
|---|---|
| `/spark profiler start` | Start sampling CPU time. |
| `/spark profiler start --timeout <seconds>` | Start and stop by itself. |
| `/spark profiler start --thread *` | Include all threads (default is the main thread). |
| `/spark profiler start --thread <name> [--regex]` | Only matching threads. |
| `/spark profiler start --interval <ms>` | Sample interval, default 4. |
| `/spark profiler start --only-ticks-over <ms>` | Only record ticks that took longer than this. Good for spikes. |
| `/spark profiler start --alloc [--alloc-live-only] [--interval <bytes>]` | Memory allocation profile (default 512 KB steps). |
| `/spark profiler start --combine-all`, `--not-combined`, `--ignore-sleeping`, `--force-java-sampler` | Other sampling options. |
| `/spark profiler info` | Is it running? |
| `/spark profiler stop [--comment <text>] [--save-to-file]` | Stop and upload the result. `--save-to-file` keeps it on disk. |
| `/spark profiler open` | Open the viewer while the background profiler keeps running. |
| `/spark profiler cancel` | Stop without uploading. |
| `/spark health` | Live dashboard link (TPS, CPU, memory, disk). |
| `/spark health upload` | Static report. |
| `/spark health show [--memory] [--network]` | Text report in chat or console. |
| `/spark tps` | TPS, MSPT, CPU. |
| `/spark ping [--player <name>]` | Player latency. |
| `/spark tickmonitor [--threshold <percent>] [--threshold-tick <ms>] [--without-gc]` | Toggle a monitor that reports slow ticks. |
| `/spark gc` | GC history. |
| `/spark gcmonitor` | Toggle GC reports. |
| `/spark heapsummary` | Upload a summary of objects in memory. |
| `/spark heapdump [--compress gzip\|xz\|lzma]` | Write a `.hprof` file to disk. Large. |
| `/spark activity [--page <n>]` | What spark did recently. |

Per-subcommand nodes: `spark.profiler`, `spark.health`, `spark.ping`, `spark.tps`, `spark.tickmonitor`, `spark.gc`, `spark.gcmonitor`, `spark.heapsummary`, `spark.heapdump`, `spark.activity`.

`/spark tps` also replaces the server's own `/tps` (see `overrideTpsCommand`).

TPS 20 and MSPT 50 or less is healthy. Anything over 50 ms per tick means lag.

## Configuration: `plugins/spark/config.json`

The file is not created by default. The server has created it with:

```json
{
  "backgroundProfiler": true
}
```

| Key | Default | Effect |
|---|---|---|
| `backgroundProfiler` | `true` | Profiles continuously from start. You can open or stop it any time. |
| `backgroundProfilerInterval` | `10` | Background sample interval in ms. |
| `backgroundProfilerEngine` | `"async"` | `"async"` or `"java"`. On this Windows test machine the console reports async is not supported and it uses the Java engine automatically. |
| `viewerUrl` | `https://spark.lucko.me/` | Viewer link base. Must end with `/`. |
| `bytebinUrl` | `https://spark-usercontent.lucko.me/` | Where profiles are uploaded. |
| `bytesocksHost` | `spark-usersockets.lucko.me` | Live viewer connection host. |
| `overrideTpsCommand` | `true` | Replace the server `/tps` with spark's. |
| `disableResponseBroadcast` | `false` | If `true`, command output is not sent to every online admin with spark permission. |

Two more settings work only as Java system properties (`-Dspark.<name>=...`): `maxStackDepth` (default `300`), `continuousProfilingHistorySize` (default `60` windows of about a minute; lower it if uploads fail with HTTP 413), and `useOsTmpDir` (default `false`). Any key can also be set as an environment variable `SPARK_<NAME>` in capitals.

## Data files

- `plugins/spark/config.json`.
- `plugins/spark/tmp/` holds temporary data. Files ending in `.tmp` can be deleted while the server is stopped.
- Heap dumps from `/spark heapdump` are large. Delete them after use.

## Interactions

- Uploads go to `spark.lucko.me` servers. Profile links contain server information such as plugin names. Only share them with staff.
- With a permission plugin, give `spark` only to admins. Players see nothing from spark.
- spark shows mob-level and generation code from DwellerCore, DwellerBiomes and DwellerStructures by plugin name in the viewer, so you can tell which plugin is slow. See [Technical notes](../technical.md).

## Recommended for this server

- Keep the background profiler on. It costs little and lets you open a profile after a lag complaint.
- Give admins `spark`:

```
/lp group admin permission set spark true
```

- To test world generation, stand at an unexplored area, run `/spark profiler start --timeout 120`, fly around, then open the link. Look for `ChunkMap`, `ChunkGenerator` and the plugin names.
- Use `--only-ticks-over 100` to catch single big spikes.

## Common problems

| Problem | Fix |
|---|---|
| Upload fails (HTTP 413) | Profile too big. Shorten the run, or lower `continuousProfilingHistorySize`. |
| No internet for upload | Use `--save-to-file`. |
| `/tps` output looks different | spark replaced it. Set `overrideTpsCommand: false`. |
| "async-profiler not supported" | Normal on some systems. The Java engine is used. |

## Links

- [spark docs](https://spark.lucko.me/docs)
- [Command usage](https://spark.lucko.me/docs/Command-Usage)
- [Configuration](https://spark.lucko.me/docs/Configuration)
- [Paper profiling guide](https://docs.papermc.io/paper/profiling)

Related: [Index](../index.md), [Troubleshooting](../troubleshooting.md).
