# Key-ARCA 0.2.1 — new application name

The Windows archiver is now named **Key-ARCA**, with the new executable
**Key-ARCA.exe**. The application title, branding text, assembly metadata,
license/contact documents and portable package names use the new name.
The existing archive-chest logo is retained.

The `.arca` extension and ARCA v1 container are unchanged. ZIP, 7z and ARCA
archives from the previous version remain supported, including encrypted
archives. This release changes the application identity, not the archive format.

Release packaging now reads the version from the same build property as the
application. Published versions remain frozen; each update gets a new package.

## Download and use

Download **Key-ARCA-0.2.1-win-x64.zip** from this release's Assets, extract all
files and run **Key-ARCA.exe**. Keep the extracted files together. This is a
portable Windows x64 package with .NET included, not an installer. Install an
update into a separate folder and update any shortcuts. There is no automatic
updater. `SHA256SUMS.txt` contains the Windows ZIP's SHA-256 checksum.

## Preview status

This remains an unsigned freeware preview for Windows 10/11 x64. The ARCA format
is experimental and does not consistently compress better than optimized 7z.
The existing safety protections, eleven languages and compression methods are
retained. There is no RAR/ISO extraction, split/join, damaged-archive repair or
Explorer shell integration in this version. The README describes resource
limits, encryption compatibility and the full feature set.

The original application source remains proprietary and private. Required
third-party notices and corresponding sources are included under `licenses`.
The public GitHub repository contains distribution documentation and branding.
The original application has not been obfuscated in this release.

Contact: [FFFNewsNow@gmail.com](mailto:FFFNewsNow@gmail.com).
