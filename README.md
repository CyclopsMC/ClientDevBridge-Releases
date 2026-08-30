# ClientDevBridge-Releases

A **static Maven repository** for [ClientDevBridge](https://github.com/CyclopsMC/ClientDevBridge),
served by GitHub Pages at:

```
https://cyclopsmc.github.io/ClientDevBridge-Releases
```

Nothing here is written by hand. Every commit is made by the CI of
[CyclopsMC/ClientDevBridge](https://github.com/CyclopsMC/ClientDevBridge), which publishes into a
checkout of this repository on every push to a `master*` branch. All three Minecraft lines publish
into the same tree; their artifact ids carry the Minecraft version, so they never collide.

## Why this exists rather than GitHub Packages

The rest of the Cyclops artifacts live in the CyclopsMC GitHub Packages Maven, which requires a
token **even for public packages**. ClientDevBridge is a development tool whose selling point is
that a mod repository needs no setup to use it, so it is published somewhere anonymously readable
instead. `clientdevbridge-cli` injects this repository into a consumer's build at launch, with no
credentials block.

## Using it directly

You do not normally need to: `clientdevbridge start` adds this repository itself. To depend on a
build by hand:

```groovy
repositories {
    maven {
        url = 'https://cyclopsmc.github.io/ClientDevBridge-Releases'
        content { includeGroup 'org.cyclops.clientdevbridge' }
    }
}

dependencies {
    // <minecraft version>-<loader>, e.g. 26.2-neoforge or 1.21.1-fabric
    modLocalRuntime 'org.cyclops.clientdevbridge:clientdevbridge-1.21.1-fabric:+'
}
```

`+` resolves through each artifact's `maven-metadata.xml`, which Gradle writes and merges on every
publish. Restricting the repository to `org.cyclops.clientdevbridge` keeps it out of the way of
everything else your build resolves.

The empty `.nojekyll` file at the root turns off Jekyll processing: without it GitHub Pages would
skip paths beginning with an underscore and try to render files it has no business touching.

## Layout

```
org/cyclops/clientdevbridge/
  clientdevbridge-<mc>-<loader>/
    maven-metadata.xml              every published version
    <version>/
      clientdevbridge-<mc>-<loader>-<version>.jar
      ...-sources.jar, ...-javadoc.jar, ...-deobf.jar, ...pom, and checksums
```

Dev-only, by construction: the mod is inert unless launched with
`-Dclientdevbridge.enabled=true`, and its server binds `127.0.0.1` only. It is not meant to ship to
players.
