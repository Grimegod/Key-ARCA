<div align="justify">

# Key-ARCA security model and encryption

This document describes implemented protections and their limits in Key-ARCA
0.6.0. It is a technical description, not an independent security audit or
certification. The current Windows preview is unsigned.

## What the application protects

Password-protected archives are intended to keep their contents confidential
from someone who obtains the archive without its password. Extraction checks
address unsafe paths, links, conflicting names, excessive expansion and damaged
data. The application preserves original inputs and creates fresh extraction
folders instead of merging over existing files.

The application runs locally, without accounts, telemetry or runtime network
calls. Website hosting and downloads are supplied separately by GitHub.

## Protection differs by archive format

| Format | Password-protected contents | File names | Implementation |
| --- | --- | --- | --- |
| ZIP | Optional AES-256 | Remain visible | Official 7-Zip engine; needs an AES-ZIP-compatible reader |
| 7z | Optional AES-256 | Encrypted when a password is used | Official 7-Zip engine |
| ARCA v1 | Optional AES-256-GCM | Encrypted with the manifest | Experimental Key-ARCA container |

The ARCA parameters below apply specifically to ARCA, not to ZIP or 7z.
New passwords are subject to the application's minimum length of 12 characters.
The current ZIP writer accepts printable ASCII passwords; choose 7z or ARCA
for Unicode passwords. Keep passwords separately: the application cannot
recover a lost archive password.

## ARCA v1 cryptographic parameters

- Key derivation: PBKDF2-HMAC-SHA256, 600,000 iterations, 32-byte derived key.
- Password encoding: strict UTF-8.
- Salt: 16 random bytes per encrypted archive.
- Nonces: an 8-byte random archive prefix followed by a 4-byte record index.
- Encryption: AES-256-GCM with 16-byte authentication tags.
- Additional authenticated data binds the format marker, version, record index,
  original length and codec identifier.
- The encrypted manifest contains file names, directory structure and metadata.
- SHA-256 checks cover stored manifest bytes and decoded files/chunks/segments.

Authentication failure is reported without distinguishing a wrong password
from corrupted encrypted data. This does not promise resistance to every
attack. Weak or reused passwords can be guessed offline. The full layout,
limits and reader rules are in [ARCA-FORMAT.md](ARCA-FORMAT.md).

## Password handling and temporary files

Passwords pass to the worker through redirected stdin and native callbacks,
not command-line arguments or operation job files. Temporary UTF-8 password
buffers and the ARCA derived key are cleared when their use finishes. Passwords
can still exist in managed strings, UI controls, process memory or native code
during an operation; complete forensic erasure is not claimed.

Archive creation uses staging and verifies the new archive before committing
it. Cancellation and ordinary failures remove operation staging. Power loss,
forced termination, storage errors or locked files can leave temporary folders.
Recovered files and the ZIP created by Recover / repair are unencrypted.

## Extraction checks and resource limits

Paths are checked for traversal, absolute/UNC paths, Windows device names,
alternate data streams, control/bidirectional characters and naming collisions.
Recognized links and reparse-point destinations are rejected. Entries are
written through application-chosen bounded streams, not native-selected paths.

The preview limits entry counts, path depth, expanded bytes and expansion ratio.
Its worker has memory, CPU and operation-time limits. Legitimately very large
or highly compressible archives can be rejected by these conservative bounds.
ISO and TAR verification checks readability; it does not provide per-file
cryptographic authentication. Archive integrity checking is not malware scanning.

## Boundaries of this model

The worker runs with the user's permissions and is not an operating-system
security sandbox. These checks do not defend against a compromised operating
system, another hostile process with equivalent permissions racing filesystem
changes, password capture or unknown vulnerabilities in native decoders.

SHA-256 download values help detect byte differences against the published
manifest. They are not digital publisher signatures and do not prove that a
file is malware-free. If the download site and its checksum are both replaced,
matching the replacement checksum does not identify the original publisher.

ARCA v1 remains experimental. Keep originals or independent backups and use
ZIP/7z when interoperability is required. No universal compression advantage,
independent audit, compliance accreditation or code-signing certificate is claimed.

## Reporting a concern

Contact **FFFNewsNow@gmail.com** with the application version and reproduction
steps using synthetic files. Do not send passwords, private documents, access
tokens or confidential archives. Public Issues are appropriate for ordinary
bugs; security-sensitive details should be sent privately. No guaranteed
response time is stated for this preview.

</div>
