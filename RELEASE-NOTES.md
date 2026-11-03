# Key-ARCA 0.3.0 — RAR/ISO, verified parts and archive recovery

## New

- Read/extract single-file RAR4/RAR5, including supported encrypted data and
  encrypted headers; read ISO9660/Joliet and readable UDF images.
- Split existing archives/files by MiB per part or a chosen count. Join from
  the manifest or any numbered part. Each part and the whole saved file are
  verified with SHA-256. Originals and parts are retained; output uses a new folder.
- Recover intact files from damaged ZIP/7z/RAR, create a new verified ZIP and
  write a recovery report. ZIP local headers can be scanned when central
  metadata is unreadable, subject to documented limits.
- New Split & join tab and Recover / repair action, translated into all eleven
  interface languages. Existing language preferences and branding are retained.

## Boundaries that matter

Key-ARCA parts use Key-ARCA.parts.json and numbered .arcapart files. Send every
file in the parts folder. They are byte parts, not native WinRAR volumes.
Native multipart RAR sets are rejected. SHA-256 checks detect accidental
corruption; an unsigned manifest does not authenticate its sender.

Recovery saves **unencrypted** files and Recovered.zip in a new folder, even
for encrypted input. It retains only decodable files passing integrity and
size checks. It does not reconstruct missing bytes or use recovery records.
7z/RAR recovery needs readable metadata; ZIP fallback supports Store/Deflate
with known 32-bit sizes. ISO/ARCA repair is not included. Read Recovery-report.json;
a partial result is labelled and unreadable metadata can hide uncounted files.
The repaired ZIP uses Store and may be larger. ISO verification reports
readability, since ISO has no per-file content checksums.

## Safety and validation

Path/link/collision, actual-byte, expansion and worker limits remain enforced.
Cancellation removes owned staging; no existing output is merged or overwritten.
Link targets and POSIX attributes are now exposed by the patched LGPL wrapper
for validation. Updated corresponding source is included under licenses.

141/141 native integration/security tests passed, including independent public
RAR fixtures, synthetic ISO images, damaged ZIP/7z/RAR, missing/modified parts,
4/5/6 balanced parts and a streamed 500,000,000-byte round trip. Large temporary
test data was removed. Portable build verification is recorded separately.

## Download

Download **Key-ARCA-0.3.0-win-x64.zip**, extract all files and run **Key-ARCA.exe**.
This remains an unsigned portable Windows 10/11 x64 freeware preview, with .NET
included. No installer or automatic updater is included. The original source
remains proprietary and private. SHA256SUMS.txt contains the public ZIP checksum.
Full instructions: docs/ARCHIVE-TOOLS.md and README.md in the package.

Contact: [FFFNewsNow@gmail.com](mailto:FFFNewsNow@gmail.com).
