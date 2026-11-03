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
