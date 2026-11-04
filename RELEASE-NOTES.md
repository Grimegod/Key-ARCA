# Key-ARCA 0.4.1 — original folder names and faster ARCA creation

## Fixes

- Save As suggests the original folder name for ZIP, 7z and ARCA, including
  dots and Unicode characters. A folder named Documents.2026 produces
  Documents.2026.arca by default. The folder name inside the archive is
  preserved. The initial save location is beside the selected folder.
- Selecting ARCA preserves the chosen compression profile instead of forcing
  Smaller. Balanced is the initial choice. Smaller remains an explicit option
  for a possible size gain at a higher processing cost.
- Reuse read/chunk buffers across files, avoiding approximately 576 KiB of
  repeated allocation per source file.
- Query filesystem attributes once per ancestor instead of repeating existence
  and attribute probes. Every ancestor is still checked; inaccessible paths
  fail closed. Link, path, integrity, cancellation and no-overwrite checks remain.

## Validation and measured scope

163/163 native integration/security checks passed, including Unicode/dotted
folder names, all three creation formats, cross-file buffer boundaries,
bounded allocations, junctions below missing paths and existing ARCA safety
cases.

On frozen synthetic inputs, with Balanced and built-in verification in both
versions: 2,000 small files took 25.720 s in 0.4.0 and 4.744 s in 0.4.1;
600 text/code files took 8.176 s and 2.055 s. The resulting same-profile
archives were byte-for-byte identical. These are single-run measurements on
this computer, not a universal speed guarantee.

Changing the previous automatic Smaller default to Balanced is a separate
tradeoff: the tested text/code archive was 19.02% larger and the tiny-file
archive 2.26% larger than Smaller. Both profiles remain available.

ARCA v1 remains experimental and format-compatible. The application remains
an unsigned freeware preview. No existing public release assets are replaced.

---

# Key-ARCA 0.4.0 — WinRAR volumes, installer and more readers

## New

- Open multipart WinRAR archives directly. Keep all parts in one folder and
  select any `.part1.rar` or `.part01.rar` part. The first and subsequent parts
  are found automatically, including solid sets. Missing parts are reported
  by filename; incomplete output is discarded.
- Windows per-user installer: **Key-ARCA-0.4.0-Setup-x64.exe**. Includes .NET,
  Start menu shortcut, optional desktop shortcut, updates and Windows Installed
  apps uninstall. No administrator access is needed. User archives/preferences
  are retained on removal, and nonempty unrelated folders are refused.
- Read/extract TAR, GZIP, BZIP2 and XZ. Compressed TAR files use two steps:
  decompress `.tar.gz`/`.tgz`, then open the resulting `.tar`.
- Keep ZIP/7z/ARCA creation, RAR/ISO reading, verified Split & join and
  best-effort ZIP/7z/RAR recovery from 0.3.0. New status/errors are translated
  into all eleven interface languages.

## Safety and validation

158/158 native integration/security tests passed. New independent RAR5 fixtures
cover padded/unpadded names, selection from a middle part, missing first/middle/
last volumes, solid sets, locked inputs and rejected external volume requests.
TAR/GZ/BZ2/XZ tests check exact bytes, output conflicts and expansion limits;
unsafe TAR paths, symlinks and hardlinks are rejected. Unknown BZIP2 sizes are
shown explicitly and limited by actual bytes while reading.

Paths, links, collisions, expansion and worker limits remain enforced. Native
RAR volume access uses only validated, held streams; native filesystem fallback
is disabled. Corresponding patched LGPL dependency source is included.

## Boundaries

RAR and the additional formats are read-only. Key-ARCA creates its own verified
byte parts using `Key-ARCA.parts.json` and numbered `.arcapart` files; it does
not create WinRAR volumes. Send the manifest and every part. SHA-256 detects
corruption but does not authenticate the sender.

Recovery retains decodable intact files and creates an **unencrypted** new ZIP
and report, even from encrypted input. It does not reconstruct missing bytes
or use WinRAR recovery records. Solid-block damage can affect several files.
ISO/ARCA repair is not included. Read `Recovery-report.json`.
ISO and TAR verification report readability rather than per-file checksums.

## Download

Use **Key-ARCA-0.4.0-Setup-x64.exe**, or extract all files from the portable
**Key-ARCA-0.4.0-win-x64.zip** and run **Key-ARCA.exe**. Both are unsigned
Windows 10/11 x64 freeware previews; Windows may display a publisher/reputation
warning. No automatic updater is included. The original application source
remains proprietary and private. `SHA256SUMS.txt` contains both download hashes.
See README.md and docs/ARCHIVE-TOOLS.md in the package for full instructions.

Contact: [FFFNewsNow@gmail.com](mailto:FFFNewsNow@gmail.com).
