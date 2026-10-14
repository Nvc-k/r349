# Modpack Master Changelog & Engineering Report

---

### Performance Engine & Mod Optimization
* **Optimization Suite Integration**:
  * Evaluated and verified core optimization mods including Sodium, Lithium, FerriteCore, ImmediatelyFast, BadOptimizations, Memory Leak Fix, and ModernFix.
  * Configured asynchronous chunk processing and rendering pipeline optimizations with Voxy and Enhanced Block Entities.
  * Verified memory allocations and garbage collection behavior under Java 25 runtime to stabilize frame pacing and prevent GC spikes.
* **Settings & Profiling Tuning**:
  * Tuned client rendering parameters inside [`options.txt`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/options.txt) to balance visual fidelity and high refresh-rate stability.
  * Optimized entity distance scaling, simulation distance, chunk loading concurrency, and particle budgets.
  * Cleared stale particle region caches and temporary generation logs to eliminate profile bloat.

---

### Texture & 3D Model Engineering

* **Cute & Better Boats Water Mask Reconstruction**:
  * **Issue**: Fresh Animations extensions (`FA+Objects-v2.1.2.zip` and `FA+All_Extensions-v1.9.2.zip`) shipped an unlinked `boat_patch.jem` with hardcoded translation offsets (`water_patch.tz: 15.5`). Because boat hull meshes were overridden by Cute Boats, animation variables failed to initialize, projecting a rectangular void 15.5 blocks into open water.
  * **Solution**: Cleaned conflicting CEM scripts from the Fresh Animations extension archives and restored the inner hull occlusion mask (`bottom_no_water`) inside [`Cute and Better Boats.zip`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/resourcepacks/Cute and Better Boats.zip), ensuring boats sit naturally in water without visual holes or hull flooding.

* **Glowing 3D Armor Full Geometric Overhaul**:
  * **Normalized Inverted Geometry**: Fixed 117 inverted `from > to` coordinate definitions across `models/item/armor/` and `models/item/enchanted/armor/` that caused backface culling errors and invisible armor plates.
  * **CEM Normal Inversion Fix**: Rebuilt negative-width model entries in `leggings.jem` (`-5.6`, `-7.6`) and `boots.jem` (`-7.6`) into standardized positive dimensions (`width: 4`, offset `x: -3.9`) with UV East/West face mirroring.
  * **Iris Shader Bypass & Z-Fighting Mitigation**: Injected `"light_emission": 15` and `"shade": false` attributes into enchanted armor JSON definitions while expanding outer boundary elements by `0.05` units, preventing depth-buffer fighting under Iris shader passes.
  * **Punchy Handheld Transforms**: Realigned first-person and third-person transformation matrices (`[0, -90, 25]` right hand, `[0, 90, -25]` left hand) to neutralize unintended rotation flips introduced by combat animation overrides.
  * **Armor Trims Restoration**: Reconstructed `minecraft:trim_material` model predicates in armor item JSONs for GUI and drop displays; restored missing trim color palettes (`amethyst`, `emerald`, `lapis`, `quartz`, `redstone`, `resin`); normalized texture alpha channels from 252 to 255 to restore native `armor_trims.json` atlas `paletted_permutations` generator matching.

* **Compatibility & Priority Hierarchy**:
  * Established top load-order priority for critical 3D model packs in [`options.txt`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/options.txt) (`Cute and Better Boats.zip`, `Glowing 3D Armor.zip`, `Fishing_Rod_3D.zip`, `Fyoncle's 3D Trims.zip`, `§aBetter Fresher 3D Books v1.5.1.zip`).
  * Reconciled conflicts between Punchy, Fresh Moves, and custom item model overrides.

---

### Modpack Packaging & Distribution Architecture

* **Official Modrinth Indexing**:
  * Automated cryptographic SHA-1/SHA-512 hashing across all 249 installed mod jars.
  * Queried Modrinth API (`/v2/version_files`) to index 244 official mods directly into [`modrinth.index.json`](file:///G:/- GAMES/Modrinth App/profiles/Modpack_Export/Modpack_Export.mrpack).
  * Reduced package upload size from over 750 MB down to **293.77 MB** (comfortably under the 500 MB Modrinth limit).
* **Custom & Local Overrides Preservation**:
  * Bundled all 5 custom/exclusive mods into `overrides/mods/`:
    * [`decobridge-fabric-26.1.2-1.0.0.jar`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/mods/decobridge-fabric-26.1.2-1.0.0.jar) (containing complete custom Decocraft 3D assets)
    * [`ancient_remnants-fabric-26.1-1.3.1.jar`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/mods/ancient_remnants-fabric-26.1-1.3.1.jar)
    * [`atmospherics-2.6.6-mc-26.1.jar`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/mods/atmospherics-2.6.6-mc-26.1.jar)
    * [`concrete-slabs-26.1b.jar`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/mods/concrete-slabs-26.1b.jar)
    * [`Epic Witch Huts v1.3.1.jar`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/mods/Epic Witch Huts v1.3.1.jar)
  * Preserved all active resource packs, custom CEM fixes, active shader packs, world datapacks, FancyMenu configurations, [`options.txt`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/options.txt), and [`servers.dat`](file:///G:/- GAMES/Modrinth App/profiles/server (1)/servers.dat).
  * Excluded orphaned debug folders and uncompressed backup shaders to ensure optimal archive hygiene.
