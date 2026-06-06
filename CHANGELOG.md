# Changelog

Maintained fork of screenshot-basic.

## Unreleased

### Fixed
- Node logged a DEP0005 deprecation warning on every screenshot upload. The
  bundled `formidable@1.2.2` (pulled in by `koa-body@4`) still called the old
  `Buffer()` constructor in `MultipartParser.initWithBoundary` and the base64
  part decoder.

  formidable 1.x never moved to the safe Buffer APIs, and the versions that did
  (formidable 2/3) need koa-body 5/6 and webpack 5, which FiveM's bundled
  webpack 4 cannot compile. So the fix stays on formidable 1.x and patches the
  four calls through `patch-package`, reapplied on `postinstall`:
  - `new Buffer(n)` to `Buffer.alloc(n)` (boundary and lookbehind)
  - `new Buffer(str, 'base64')` to `Buffer.from(str, 'base64')` (two base64 decodes)

  No runtime change: the boundary buffer is fully written before use, and the
  base64 calls take strings, never the uninitialized-number form.

Author: MrWhitee
