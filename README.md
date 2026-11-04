# Key-ARCA 0.4.1

Freeware archiver for Windows 10/11 x64, with ZIP, 7z and experimental ARCA support.
Free for personal, educational and commercial use. No ads, accounts or telemetry.

## Download

[Download Key-ARCA 0.4.1 for Windows x64](https://github.com/Grimegod/Key-ARCA/releases/download/v0.4.1/Key-ARCA-0.4.1-Setup-x64.exe)

Choose **Key-ARCA-0.4.1-Setup-x64.exe** for the installer, or use the
[portable ZIP](https://github.com/Grimegod/Key-ARCA/releases/download/v0.4.1/Key-ARCA-0.4.1-win-x64.zip).
Both include .NET; no Git, Visual Studio or separate runtime is required.

1. Download and run **Key-ARCA-0.4.1-Setup-x64.exe** from the release Assets.
2. Choose a dedicated application folder. Installation is per user without administrator access.
3. Open Key-ARCA from the Start menu. A desktop shortcut is optional.

Install a newer release into the same folder to update. Remove the installed
app through Windows **Installed apps**; user archives and preferences are kept.
The installer refuses nonempty unrecognized folders and newer-version downgrades.
The saved language is under `%LOCALAPPDATA%/Key-ARCA/Preferences`.

For portable use, extract **all** ZIP files and open **Key-ARCA.exe**. Keep its
DLLs, languages and licenses together. Update into a new folder; remove only
the portable application files when finished. Keep your own archives.
There is no automatic updater.

GitHub's automatically generated **Source code** downloads contain public
website documentation. Use Setup or the Windows ZIP to get the application.

## Features

- Create, preview, verify and extract ZIP, 7z and `.arca` archives.
- Open/extract RAR4/RAR5, including multipart WinRAR sets, plus ISO, TAR, GZ, BZ2 and XZ.
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

In **Open & extract**, choose a damaged ZIP, 7z or RAR, enter its
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

Version 0.4.1 is an **unsigned preview**. Windows may show a publisher or
reputation warning; the application is not represented as digitally signed.

The application is now named **Key-ARCA**. Existing `.arca` archives keep their
extension and format and remain supported, including encrypted archives.
The ARCA format is experimental and currently requires Key-ARCA to open.
Keep originals or independent backups. It does **not** consistently compress
better than optimized 7z. The [format description](https://github.com/Grimegod/Key-ARCA/blob/main/ARCA-FORMAT.md) and
[complete compression measurements](https://github.com/Grimegod/Key-ARCA/blob/main/COMPRESSION-BENCHMARKS.md) explain its
behavior and limits, including cases where 7z is smaller.

This version does not include Explorer context menus or automatic file associations.
RAR/ISO creation and recovery records are not supported.
ISO and TAR verification reports readability rather than per-file content checksums.
Keep all WinRAR parts in one folder and select any part; missing parts are named.
Compressed TAR files require two steps: decompress .tar.gz/.tgz, then open the TAR.
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

## Creation fixes in 0.4.1

Save As uses the selected folder name. ARCA starts with Balanced and keeps
your profile choice when formats change. Smaller is optional and takes
longer. Faster metadata checks preserve path/link protection and the
selected-profile archive bytes. See the release notes and compression
measurements for the synthetic test results and size/time tradeoffs.
