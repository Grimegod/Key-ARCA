# ARCA archivator 0.2.0 — experimental ARCA format

A Windows x64 ZIP/7z/ARCA archiver. Free for personal, educational and commercial use.
The original application source remains proprietary. See
`licenses/Freeware-License.txt` and `licenses/THIRD-PARTY-NOTICES.md` in the ZIP.

Contact: [FFFNewsNow@gmail.com](mailto:FFFNewsNow@gmail.com).

## Included

- New independent `.arca` format with content-defined deduplication, grouping
  by extension and adaptive Brotli/LZMA2/PPMd compression. ARCA is experimental,
  currently requires this application, and does not always compress better than 7z.
- Optional ARCA AES-256-GCM encryption hides contents and file names. SHA-256
  checks protect files, chunks and segments; bounded metadata and strict layout
  validation reject malformed input before extraction commits.
- ARCA format notes and a complete measured comparison, including losses,
  are included under `docs`. A synthetic duplicate-document case was 18.2%
  smaller than the best tested solid 7z result; the text/code corpus was 0.4%
  larger than 7z/PPMd. These are examples, not universal compression claims.
- Create ZIP and 7z archives, preview contents, extract and check integrity.
- Optional AES-256 encryption; encrypted 7z archives also hide file names.
- English plus Romanian, Spanish, French, German, Portuguese, Italian,
  Simplified Chinese, Japanese, Arabic and Hindi.
- Original files retained, no extraction overwrites, validated paths and
  duplicate-name checks, expanded-data limits and a resource-limited worker.
- Offline operation with no accounts, advertisements or telemetry.
- Portable self-contained .NET build; extract all files and run ArcaArchivator.exe.
- Original archive-chest logo, embedded Windows/taskbar icon and desktop ICO.
- Indigo, mint and apricot interface with rounded controls, clearer empty states,
  optional password/advanced sections and switchable subtle animations that
  respect Windows reduced-motion preferences.

## Current limits

This is an unsigned preview. Windows 10/11 x64 is the target platform. Existing
solid 7z archives can be slow to extract. There is no repair/recovery feature,
Explorer shell extension, split-volume support or in-place archive editing.
The worker is resource-limited, not an OS security sandbox. The README describes
the protection boundaries and conservative archive limits.

ZIP AES passwords use printable ASCII characters with this engine; choose 7z or ARCA
for Unicode passwords. Passwords for new archives need at least 12 characters.
AES-encrypted ZIP requires a compatible reader.

## Download and use

Download `ARCA-archivator-0.2.0-win-x64.zip` from this release's Assets,
extract all files and run **ArcaArchivator.exe**. Keep the extracted files
together. This is a portable package, not an installer, and includes .NET.
`SHA256SUMS.txt` contains the Windows ZIP's SHA-256 checksum.

The public repository contains distribution information and documentation.
The original application source remains private.
