# Unstable Generation 1.21.1 Fabric Backport

This repository backports the provided `unstable-generation-1.0.7-1.21.11.jar` binary to a Fabric loader jar that advertises Minecraft **1.21.1** compatibility.

The upstream jar is already compiled. Because only the jar was available, this project does not recreate the original Java sources. Instead, the Gradle build repackages the upstream classes and resources, patches `fabric.mod.json` for the 1.21.1 Fabric runtime, and validates the resulting artifact structure.

## What the backport changes

- Changes the mod version to `1.0.7+1.21.1`.
- Changes the Minecraft dependency from `~1.21.11` to `>=1.21.1 <1.21.2`.
- Keeps Java 21, Fabric Loader `>=0.16.6`, and Fabric API dependencies.
- Removes the upstream `fabric-datagen` entrypoint because the provided jar references `net.unstablegeneration.UnstableDataGenerator` but does not include that class. This avoids a missing-entrypoint loader error for the backported jar.
- Preserves the original compiled classes, mixins, assets, and data packs.

## Requirements

- Java 21 or newer.
- Gradle 8.14+ or a compatible system Gradle installation.
- Minecraft 1.21.1 with Fabric Loader 0.16.6+.
- Fabric API for Minecraft 1.21.1, for example `fabric-api-0.116.11+1.21.1` or newer for the 1.21.1 line.

## Build guide

From the repository root, run:

```bash
gradle clean build
```

The backported jar will be written to:

```text
build/libs/unstable-generation-1.0.7+1.21.1.jar
```

For a faster build that only creates the jar without running validation:

```bash
gradle backportJar
```

To run the structural validator explicitly:

```bash
gradle validateBackport
```

## Install guide

1. Build the jar with `gradle clean build`.
2. Install Minecraft **1.21.1** with Fabric Loader **0.16.6+**.
3. Put `build/libs/unstable-generation-1.0.7+1.21.1.jar` in your `.minecraft/mods` folder.
4. Put a Fabric API jar for Minecraft **1.21.1** in the same `mods` folder.
5. Start the Fabric 1.21.1 profile.

## Validation notes

`gradle validateBackport` checks that:

- The generated jar contains the patched `fabric.mod.json`.
- The Minecraft dependency is exactly `>=1.21.1 <1.21.2`.
- The missing upstream datagen entrypoint is removed.
- The main entrypoint classes are present.
- Every mixin listed in `unstablegeneration.mixins.json` has a matching class 