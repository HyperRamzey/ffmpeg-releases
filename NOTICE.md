# NOTICE — binaries produced by this pipeline are NON-REDISTRIBUTABLE

The FFmpeg binaries published in this repository's releases are built
with:

```text
--enable-gpl --enable-version3 --enable-nonfree
```

and link, among others:

- GPL libraries: x264, x265, xvid, dav1d, ... (require GPL compliance
  on redistribution), and
- the **nonfree** Fraunhofer FDK-AAC encoder (`--enable-libfdk-aac`).

Under the FFmpeg licensing model the resulting binaries are "nonfree
and unredistributable" (see the FFmpeg configure banner). They are
published here solely as a personal build artifact of the repository
owner for personal use.

**Do not mirror, bundle, ship, or redistribute these binaries or the
release zips.**

Additionally, the `aac_at` (Apple AudioToolbox AAC) support is provided
through the wat4ff wrapper, which at runtime loads Apple's proprietary
CoreAudio DLLs (iTunes / Apple Application Support / QTfiles64). Those
DLLs are Apple's property, are NOT included in the releases, and must
be obtained by the end user under Apple's own terms.
