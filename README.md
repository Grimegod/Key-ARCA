# Key-ARCA 0.3.0

Freeware archiver for Windows 10/11 x64, with ZIP, 7z and experimental ARCA support.
Free for personal, educational and commercial use. No ads, accounts or telemetry.

## Download

[Download Key-ARCA 0.3.0 for Windows x64](https://github.com/Grimegod/Key-ARCA/releases/download/v0.3.0/Key-ARCA-0.3.0-win-x64.zip)

This release is a **portable ZIP**, not an installer. It includes the required
.NET runtime. You do not need Git, Visual Studio or a separate .NET installation.

1. Download `Key-ARCA-0.3.0-win-x64.zip` from the release's **Assets**.
2. Right-click the ZIP and choose **Extract All**.
3. Open the extracted folder and run **Key-ARCA.exe**.
4. Keep all extracted files together. Create a shortcut to the executable if desired.

To remove the portable application, close it and delete its application folder.
Keep your own archives and extracted files. Future updates require downloading
and extracting a new release into a separate folder; there is no automatic updater.
The saved language is stored separately under `%LOCALAPPDATA%/Key-ARCA/Preferences`.
Delete that folder too if you want to remove the saved preference; keep your archives.

The automatically generated **Source code** downloads on GitHub contain this
public distribution page and documentation. Download the Windows ZIP above to
use the application.

## Features

- Create, preview, verify and extract ZIP, 7z and `.arca` archives.
- Open/extract single-file RAR4/RAR5 archives, including supported encrypted archives, and ISO images.
- Split/join existing files by part size or count, with a manifest and SHA-256 checks.
- Recover intact files from damaged ZIP/7z/RAR into a new folder, verified ZIP and report.
- Optional AES-256 encryption. 7z and ARCA can also hide file names.
- ARCA uses content-defined deduplication and chooses between Brotli, LZMA2 and PPMd.
- New archives are verified before saving; original files are retained.
- Extraction uses new folders, without merging or overwriting existing files.
- Unsafe archive paths, links, conflicting names and excessive expansion are blocked.
- Configurable expanded-size limit: 2, 10, 20 or 50 GiB; 20 GiB is the default.
- English default plus Romanian, Spanish, French, German, Portuguese, Italian,
  Simplified Chinese, Japanese, Arabic and Hindi.
- Your language choice is remembered per Windows user, including across portable updates and when the application folder is read-only. Existing portable language preferences are migrated.
- Original archive-chest logo and optional subtle animations.

## Split and join

Open **Split & join**, select an archive/file and choose **MiB per part** or
**Number of parts**. Choose a destination parent folder and select **Split & verify**.
Send every file from the new folder: `Key-ARCA.parts.json` and all numbered
`.arcapart` files. Email encoding increases attachment size; allow room below
your provider limit. The source and all parts are retained.

To rejoin, collect every part and the manifest in one folder, choose the
manifest or any part and select **Join & verify**. Output goes into a fresh
folder only after size, per-part and whole-file SHA-256 checks pass. These are
Key-ARCA byte parts; native WinRAR volumes have a different format. Splitting
preserves an archive's existing encryption. Hashes detect corruption; the
unsigned manifest does not authenticate the sender.

## Recovery and repair

In **Open & extract**, choose a damaged ZIP, 7z or single-file RAR, enter its
password if needed, choose the destination and select **Recover / repair**.
The new folder contains `Files/`, a verified `Recovered.zip` and
`Recovery-report.json` with recovered/skipped entries and limitations.

**Recovered files and the new ZIP are unencrypted.** Only intact files passing
decoding and integrity checks are kept. The original is unchanged. Missing
metadata can hide files that cannot be counted; recovery does not reconstruct
missing bytes. 7z/RAR require readable metadata. ZIP local-header fallback
supports Store/Deflate with known 32-bit sizes; encrypted/ZIP64/unknown-size
entries need readable central metadata. Solid-block damage can affect several
files. ISO and ARCA repair are not supported. The repaired ZIP uses Store and
can be larger; allow space for both extracted files and the new ZIP.

A detailed guide is included as `docs/ARCHIVE-TOOLS.md` in the Windows package.

## Preview status and limitations

Version 0.3.0 is an **unsigned preview**. Windows may show a publisher or
reputation warning; the application is not represented as digitally signed.

The application is now named **Key-ARCA**. Existing `.arca` archives keep their
extension and format and remain supported, including encrypted archives.
The ARCA format is experimental and currently requires Key-ARCA to open.
Keep originals or independent backups. It does **not** consistently compress
better than optimized 7z. The [format description](https://github.com/Grimegod/Key-ARCA/blob/main/ARCA-FORMAT.md) and
[complete compression measurements](https://github.com/Grimegod/Key-ARCA/blob/main/COMPRESSION-BENCHMARKS.md) explain its
behavior and limits, including cases where 7z is smaller.

This version does not include Explorer context menus or a Windows installer.
RAR/ISO creation, native multipart RAR sets and recovery records are not supported.
ISO verification reports readability, since ISO has no per-file integrity checksums.
General metadata such as ACLs, alternate streams and hard-link relationships
is not backed up. ARCA v1 also omits timestamps.

Safety limits include 50,000 entries, 48 path components, a 1,000× expansion-ratio
limit above 1 MiB, a worker memory cap of 1 GiB and a 30-minute operation timeout.
ARCA v1 has additional manifest/chunk limits. Solid 7z extraction can be slow.
The worker is resource-limited, not an OS security sandbox or a malware scanner.

New passwords require at least 12 characters. ZIP encryption uses printable
ASCII passwords and requires a compatible reader; 7z and ARCA support Unicode
passwords. Passwords are not passed in command-line arguments.

## License and third-party components

The original application is **proprietary freeware**. Its source is not part of
the public distribution. See `licenses/Freeware-License.txt` in the Windows ZIP.
Complete binary distributions may be shared free of charge under that license.

The portable package includes applicable third-party licenses, runtime notices
and corresponding 7-Zip/SharpSevenZip source packages under `licenses`. Those
dependencies retain their own modification, replacement and redistribution rights.
Open **License & credits** in the application to read the included notices.

## Contact

Questions, feedback and bug reports: **[FFFNewsNow@gmail.com](mailto:FFFNewsNow@gmail.com)**.

You can also [report an issue](https://github.com/Grimegod/Key-ARCA/issues).
For a bug, include the application version, Windows version, archive format and
steps to reproduce. Do not attach confidential files or passwords to public issues.
