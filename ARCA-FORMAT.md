# ARCA v1 — experimental format specification

Key-ARCA 0.2 introduces an independent `.arca` container for exact file
contents and directory structure. The format is experimental. Version 1 readers
reject other versions; an incompatible revision must use a new version number.
ZIP and 7z remain available for interchange with other applications.

ARCA combines global content-defined deduplication, grouping by extension,
bounded compression segments and adaptive selection of established codecs.
This is a new container and storage policy, not a claim to have invented Brotli,
LZMA2 or PPMd. It cannot guarantee smaller files than every ZIP or 7z setting.
See `COMPRESSION-BENCHMARKS.md` for measurements, including losses.

## Physical layout

All fixed-width integers are little-endian. The layout is a 128-byte header,
zero or more consecutive data segments, then a compressed manifest extending
exactly to end of file. No padding, trailing bytes or external files are allowed.

| Offset | Bytes | Meaning |
|---:|---:|---|
| 0 | 8 | Magic: hexadecimal `41 52 43 41 0D 0A 1A 0A` |
| 8 | 4 | Unsigned format version: 1 |
| 12 | 4 | Unsigned flags: 0, or 1 for encrypted data and manifest |
| 16 | 8 | Signed absolute manifest offset, at least 128 |
| 24 | 4 | Signed stored manifest length, including its authentication tag |
| 28 | 4 | Signed uncompressed UTF-8 JSON manifest length |
| 32 | 16 | PBKDF2 salt; writer uses zeros without encryption |
| 48 | 8 | Per-archive nonce prefix; writer uses zeros without encryption |
| 56 | 4 | PBKDF2 iterations: 600000 encrypted, otherwise 0 |
| 60 | 4 | Reserved, must be zero |
| 64 | 32 | SHA-256 of the stored manifest bytes |
| 96 | 32 | Reserved, must be zero |

The manifest is Brotli-compressed with a single complete stream. With encryption,
the compressed bytes are encrypted before storage. The stored hash detects
damage before decompression or password derivation; without encryption, hashes
provide integrity checking, not proof of the archive's author.

## Manifest

The JSON root is an object. Unknown properties, duplicate property names, invalid
JSON, comments, trailing data and a depth greater than 16 are rejected. Numbers
must fit their declared integer types. Zero numeric values, false booleans and
default members may be omitted; arrays emitted by the writer are present.

| Object | Property | Meaning |
|---|---|---|
| Root | `v` | Format version, signed 32-bit, must be 1 |
| Root | `n` | Total logical file bytes, unsigned 64-bit |
| Root | `f` | File/directory record array |
| Root | `c` | Unique chunk record array |
| Root | `s` | Segment record array |
| File | `p` | Relative path, `/` separators |
| File | `d` | True for directory; default false |
| File | `n` | File length, unsigned 64-bit; zero for directories |
| File | `h` | Whole-file SHA-256 as 64 hexadecimal characters; empty for directories |
| File | `c` | Ordered array of signed 32-bit indices into the root chunk array |
| Chunk | `s` | Signed 32-bit segment index |
| Chunk | `o` | Signed 32-bit offset in the uncompressed segment |
| Chunk | `n` | Signed 32-bit chunk length |
| Chunk | `h` | Chunk SHA-256, 64 hexadecimal characters |
| Segment | `c` | Signed 32-bit codec ID |
| Segment | `o` | Signed 64-bit absolute physical file offset |
| Segment | `n` | Signed 32-bit stored byte length, including any tag |
| Segment | `r` | Signed 32-bit raw byte length |
| Segment | `h` | Raw segment SHA-256, 64 hexadecimal characters |

Segment records cover all bytes between the header and manifest, in order.
Unique chunks consecutively cover each raw segment without overlaps or gaps.
Every unique chunk must be referenced by at least one file. The sum of a file's
referenced chunk lengths must equal its file length; all file lengths must sum
to the root total. Directories have zero bytes, an empty hash and no references.
An empty file has no chunks and the SHA-256 of the empty byte sequence.

## Chunking and codec selection

The writer orders directories first, then files by extension and path using
ordinal case-insensitive comparison. File data is split using a 64-bit gear
fingerprint: shift the state left one bit and add the table value for each byte,
wrapping at 64 bits. Fingerprints reset at each file and chunk boundary.
After 65536 bytes, a boundary occurs when the low 16 bits are zero, or when the
chunk reaches 524288 bytes. The final partial chunk is retained.

The 256-entry gear table is generated with SplitMix64 starting at hexadecimal
`415243415F434443`. Each step adds `9E3779B97F4A7C15`; the mixing multipliers are
`BF58476D1CE4E5B9` after XOR-shift 30 and `94D049BB133111EB` after XOR-shift 27,
followed by XOR-shift 31. Arithmetic wraps at 64 bits. SHA-256 identifies equal
chunks globally within one archive. No cross-archive database is needed.

Unique chunks are appended to raw segments of at most 8 MiB. A chunk is never
split across segments. Each segment chooses the smallest candidate byte stream:

| Codec | Payload |
|---:|---|
| 0 | Exact raw bytes |
| 1 | One Brotli stream, exact output length and complete input consumption |
| 2 | An XZ stream carrying LZMA2 |
| 3 | A 7z container with one unencrypted file, emitted with PPMd |

Native payloads decode to an application-owned bounded memory stream. Their
internal names never determine disk paths. The writer uses one file named
`data`, a fixed timestamp and one native compression thread. Codec 3 uses a
non-solid single-file container. It is only a segment inside the ARCA container.

**Store** uses codec 0 while still deduplicating. **Fast** compares raw and
Brotli quality 3. **Balanced** also compares XZ/LZMA2 normal with 16 MiB dictionary
and uses Brotli quality 6. **Smaller** compares raw, Brotli quality 11,
XZ/LZMA2 ultra with 64 MiB dictionary and 7z/PPMd ultra. Brotli window is 24.
Manifest compression uses Brotli quality 6. Codec trials increase creation time;
selection compares compressed payload size, not a predicted ratio.

## Optional encryption

Passwords use strict UTF-8 without Unicode normalization. PBKDF2-HMAC-SHA256,
600000 iterations and a random 16-byte salt derive a 32-byte AES key. A random
8-byte nonce prefix is generated independently for every encrypted archive.
AES-256-GCM encrypts each segment and the manifest independently, appending a
16-byte authentication tag to each ciphertext. There is no padding.

The 12-byte nonce is the prefix followed by a little-endian unsigned 32-bit
record index. Data segment indices start at 0. The manifest uses `FFFFFFFF`.
The 24-byte associated data is magic (8), version 1 (unsigned 32-bit), record
index (unsigned 32-bit), raw length (signed 32-bit) and codec (signed 32-bit).
The manifest uses codec 1 and its raw JSON length. A new key/salt/prefix is used
for each archive; record nonces are never reused within an archive.

The encrypted manifest hides names, paths, individual sizes, hashes and chunk
references. Total archive size, the existence of encryption and manifest size
remain visible. A missing/wrong password or tag failure prevents extraction.

## Reader and application limits

- Stored manifest at most 65 MiB; raw JSON at most 64 MiB, checked before allocation.
- At most 8192 segments, 262144 unique chunks and 1000000 file chunk references.
- Raw segments 1–8 MiB; stored segments at most 9 MiB; chunks 1–512 KiB.
- At most 50000 file/directory entries, 48 path components, 1024 path characters
  and 240 characters per component. Paths must pass Windows traversal, device,
  stream, duplicate/case/Unicode-normalization and file-parent conflict checks.
- Default expanded limit 20 GiB, selectable up to 50 GiB. Expansion above 1000×
  is blocked when logical output exceeds 1 MiB, including legitimate duplicates.
- Every decoded segment, chunk and complete file is SHA-256 checked. Actual
  writes are also bounded by declared sizes and the user's total limit.
- The application worker has a 1 GiB committed-memory limit, 50% aggregate CPU
  cap and 30-minute timeout. A four-segment raw cache holds at most 32 MiB.

Creation verifies the temporary archive before committing a new output name.
Extraction commits a new folder only after verification; failures and cancellation
discard staging. Existing files and original inputs are kept. Reparse-point
ancestors and source links are rejected. Download-zone metadata is propagated.

ARCA v1 does not preserve timestamps, ACLs, hard-link relationships or arbitrary
alternate streams. It has no recovery records or damaged-archive repair.
Native codecs are resource-limited, not isolated in an OS security sandbox.

## References

- 7z's existing codec and solid-compression capabilities: https://www.7-zip.org/7z.html
- .NET Brotli encoder: https://learn.microsoft.com/en-us/dotnet/api/system.io.compression.brotliencoder.trycompress?view=net-10.0
- .NET AES-GCM: https://learn.microsoft.com/en-us/dotnet/api/system.security.cryptography.aesgcm?view=net-10.0

The original ARCA implementation remains the owner's proprietary freeware source.
Publishing this format description does not change the source-code license.
