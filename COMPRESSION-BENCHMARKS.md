<div align="justify">

# ARCA v1 compression measurements

Measured on Windows x64 on 2026-10-26, using .NET 10.0.14 and the bundled
7-Zip 26.05 engine. These are exploratory measurements, not a broad ranking
of compressors. ARCA is experimental. Smaller is better in the size table.

**Result:** ARCA was 18.2% smaller than the best tested 7z on the synthetic
identical-document case. It was 0.4% larger on the text/code corpus and 2.6%
larger on the frozen source snapshot. It does not consistently beat 7z.

| Dataset | Input bytes | ZIP bytes | 7z LZMA2 bytes | 7z PPMd bytes | ARCA bytes | ARCA vs best 7z |
|---|---:|---:|---:|---:|---:|---|
| Canterbury, all 11 files | 2,810,784 | 674,635 | 483,649 | 571,477 | 483,474 | 0.036% smaller |
| Canterbury, 8 text/code files | 1,229,584 | 435,738 | 383,253 | 322,126 | 323,443 | 0.409% larger |
| Frozen ARCA source snapshot | 136,787 | 43,169 | 27,637 | 25,312 | 25,962 | 2.568% larger |
| Synthetic: five source versions | 684,223 | 222,208 | 28,155 | 86,420 | 28,729 | 2.039% larger |
| Synthetic: 100 identical documents | 42,675,400 | 13,793,422 | 126,037 | 146,256 | 103,135 | 18.171% smaller |
| Already-compressed control | 785,443 | 673,900 | 669,919 | 692,920 | 670,443 | 0.078% larger |

The 175-byte advantage on the complete Canterbury set is only 0.036%; it is
too small to justify a general superiority claim. The synthetic source-version
case favored solid 7z/LZMA2 despite ARCA deduplication. The already-compressed
control is only two particular files; it is not representative of all media.

## Configurations and verification

- ZIP: Deflate, Ultra; 7z: solid LZMA2 Ultra with 64 MiB dictionary, and solid
  PPMd Ultra with engine-default PPMd settings. Both 7z methods were tested;
  the comparison column chooses the smaller archive for each dataset.
- ARCA: Smaller profile, raw/Brotli quality 11/XZ-LZMA2 Ultra 64 MiB/7z-PPMd
  Ultra candidates per segment; 8 MiB raw segment cap and global deduplication.
- One native compression thread; the same 1 GiB worker memory cap, 50% CPU
  cap and 30-minute timeout for every method. No passwords in these tests.
- The same file bytes and relative file names are used for all methods,
  ordered by extension then path. ARCA also records directories. Native
  baseline timestamps are fixed to 2000-01-01; ARCA omits general timestamps.
- Every native output passes its integrity check, is extracted in one pass
  into a private benchmark folder, and every file is SHA-256 compared with
  its input. ARCA creation reopens the archive and verifies decoded file
  hashes against hashes collected while reading the inputs. Separate tests
  also verify ARCA extraction byte for byte, empty items and multiple segments.
- All 24 measured outputs passed. This is one trial per configuration, with
  no repeated-run statistics, peak-memory measurements or comprehensive
  tuning of every possible 7z setting. Other settings can change the result.

## Recorded elapsed times

Seconds include worker startup, creation and the integrity workflow described
above. The workflows differ: native baselines include an additional disk
extraction and source hash pass, while ARCA checks its temporary archive
before atomic commit. CPU scheduling and concurrent desktop work add noise.
These are observed operation times, not isolated compression throughput.

| Dataset | ZIP seconds | 7z LZMA2 seconds | 7z PPMd seconds | ARCA seconds |
|---|---:|---:|---:|---:|
| Canterbury, all 11 files | 2.227 | 1.379 | 1.683 | 6.942 |
| Canterbury, 8 text/code files | 1.251 | 0.775 | 0.900 | 3.064 |
| Frozen ARCA source snapshot | 0.513 | 0.475 | 0.525 | 0.770 |
| Synthetic: five source versions | 1.450 | 1.198 | 1.308 | 1.609 |
| Synthetic: 100 identical documents | 34.960 | 10.805 | 12.175 | 2.153 |
| Already-compressed control | 0.739 | 0.577 | 1.088 | 2.857 |

## Datasets and reproduction

1. The complete standard Canterbury corpus: 11 files, 2,810,784 bytes.
2. Its eight `.txt`, `.html`, `.c`, `.lsp` and `.1` files: 1,229,584 bytes.
3. A frozen local ARCA C#/XAML/project-source snapshot: 25 files, 136,787
   bytes. This snapshot precedes the final safety-test and UI edits; it is
   a benchmark fixture, not the complete release source package.
4. Five synthetic copies of that snapshot, with a short version comment
   added to selected C# files in versions 1–4: 125 files, 684,223 bytes.
5. One hundred exact copies of Canterbury `lcet10.txt`, under different
   names: 42,675,400 bytes. This intentionally demonstrates deduplication;
   it is not 100 independent documents or evidence for ordinary documents.
6. The corpus ZIP plus the application PNG logo: two files, 785,443 bytes.

No personal user files were used. Corpus contents and frozen original
application source inputs are not included in the public release.

The owner can reproduce a run with `tools/prepare_benchmark.py`, then build
`tools/KeyArcaBench/KeyArcaBench.csproj` and execute it with the project root as
its argument. Re-preparing after source changes creates a different source
fixture; compare its inventory before interpreting different results.

Local measurement run: `run-20261026-181615`. Its `results.json`, output archives,
restored native files and the frozen `input-manifest.json` remain under
`artifacts/compression-research`. Public packages include this report only.

SHA-256 identifiers for the measured inputs and engine:

- `cantrbry.zip`: `c44b686dfc137e74aba4db0540e5d6568cb09e270ba8f8411d2f9df24f39a1a6`
- `input-manifest.json`: `98e0bf34ecdc099ba8081af44743feec42a69951409debed97f32190c52f3cfc`
- `7z.dll`: `78bf6b668d75801e3aa180b4ccb16e6d8395fba80dec8a065055bb6f8dfabb82`

Primary references: [Canterbury corpus descriptions](https://corpus.canterbury.ac.nz/descriptions/)
and [7z format capabilities](https://www.7-zip.org/7z.html).

## Creation speed update — 0.4.1


Key-ARCA 0.4.1 fixes archive naming and creation performance. These are targeted
single-run measurements on frozen synthetic inputs on this Windows computer.
Creation includes the same built-in integrity verification in both versions.
Input inventories and hashes are retained privately under
artifacts/creation-performance-2026-11-04. No personal files were used.

| Synthetic input | Files | Input bytes | 0.4.0 Balanced | 0.4.1 Balanced | Speedup | Archive bytes |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Compressed.Control | 1 | 8,388,608 | 1.422 s | 1.319 s | 1.08× | 8,391,550 |
| Many.Small.Files | 2,000 | 662,890 | 25.720 s | 4.744 s | 5.42× | 424,646 |
| Text.Code | 600 | 2,396,890 | 8.176 s | 2.055 s | 3.98× | 467,818 |

All three same-profile archives are byte-for-byte identical before and after.
The optimisation reuses read/chunk buffers across files and replaces repeated
Exists/Directory.Exists/attribute queries with one attribute query per ancestor.
Every ancestor is still checked; inaccessible paths fail closed. File/segment
hashes, verification, encryption, worker limits and no-overwrite rules remain.

## Default profile correction

Selecting ARCA previously forced Smaller, which tries Brotli 11, XZ/LZMA2 and
PPMd. It now preserves the selected profile; the initial choice is Balanced.
Smaller remains available explicitly. The tradeoff must be stated separately
from same-profile engine gains:

| Input | Old automatic Smaller | New initial Balanced | Smaller bytes | Balanced bytes | Size tradeoff |
| --- | ---: | ---: | ---: | ---: | ---: |
| Compressed.Control | 14.908 s | 1.319 s | 8,391,550 | 8,391,550 | +0.00% |
| Many.Small.Files | 26.533 s | 4.744 s | 415,260 | 424,646 | +2.26% |
| Text.Code | 12.048 s | 2.055 s | 393,047 | 467,818 | +19.02% |

These results do not establish that ARCA beats ZIP or 7z. Different data and
disk conditions will produce different results. There was no format revision:
ARCA v1 compatibility is retained.

## Bounded workflow comparison — 5 November 2026

Key-ARCA 0.4.1, the bundled 7-Zip 26.05 CLI and frozen public/synthetic inputs.
One measurement per configuration; this is a bounded exploratory baseline.
The same file contents and logical paths were restored and SHA-256 checked
for all 12 outputs. Existing release binaries were used without rebuilding.

| Input | Method | Archive bytes | Create + integrity (s) | Extract (s) | Creation peak working set (MiB) | Extraction peak working set (MiB) |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| public-text-code | zip-deflate-ultra | 435,772 | 1.028 | 0.093 | 10.8 | 8.8 |
| public-text-code | 7z-lzma2-64m | 383,285 | 0.468 | 0.097 | 24.6 | 10.0 |
| public-text-code | 7z-ppmd | 322,164 | 0.493 | 0.277 | 40.1 | 38.5 |
| public-text-code | arca-smaller | 323,431 | 3.028 | 0.635 | 100.1 | 84.4 |
| synthetic-small-files | zip-deflate-ultra | 51,052 | 0.146 | 0.180 | 9.1 | 8.6 |
| synthetic-small-files | 7z-lzma2-64m | 22,478 | 0.140 | 0.180 | 10.6 | 8.6 |
| synthetic-small-files | 7z-ppmd | 22,833 | 0.150 | 0.193 | 10.2 | 9.2 |
| synthetic-small-files | arca-smaller | 26,765 | 1.135 | 0.924 | 65.6 | 56.4 |
| incompressible-control | zip-deflate-ultra | 8,389,006 | 2.623 | 0.062 | 15.0 | 8.7 |
| incompressible-control | 7z-lzma2-64m | 8,389,342 | 2.205 | 0.065 | 98.0 | 17.5 |
| incompressible-control | 7z-ppmd | 8,541,064 | 10.076 | 5.206 | 138.5 | 137.6 |
| incompressible-control | arca-smaller | 8,391,558 | 12.473 | 0.390 | 265.6 | 60.2 |

ARCA Smaller was 0.3933% larger than the best tested 7z on the public text/code
set, 19.0720% larger on the 128 small synthetic files, and 0.0264% larger on
the incompressible control. These results do not show a general ARCA advantage.

Configuration: ZIP Deflate Ultra; solid 7z LZMA2 Ultra with 64 MiB dictionary;
solid 7z PPMd Ultra with engine-default PPMd settings; ARCA Smaller. Each
compressor used one compression thread. No application Job CPU/memory caps
were applied to these directly launched benchmark processes. Input sets:
8 Canterbury text/code files (1,229,584 bytes), 128 frozen synthetic small files
(42,258 bytes), and one 8 MiB synthetic incompressible control. The control is
random data, not a representative sample of PDF, Office, photos or video.

Creation time includes one integrity workflow: a separate 7z t for native
outputs, and built-in decoded SHA-256 verification for ARCA. Timings include
process startup and filesystem work; they are not isolated codec throughput.
Extraction is timed separately; the common external SHA-256 comparison is
recorded separately in the private raw results. ARCA ordered extraction returns
the original root folder itself; the harness restores that prefix when comparing
logical paths. An initial harness root-prefix mismatch was corrected without
changing the product; the first three completed native cases were retained.

Memory is the sampled Windows peak process working set reported by
GetProcessMemoryInfo, including the runtime, not committed/virtual memory.
Native CLI file ordering/metadata and ARCA ordering/metadata differ. There are
no repeated-run confidence intervals, password cases or exhaustive tuning.
Do not generalize the small size difference or the short timings to all inputs.

On the small-file case, the encoded ARCA manifest occupies 6,249 of 26,765
archive bytes (23.35%). This supports measuring metadata compression and
codec-trial costs before changing the format. The wider A01 matrix remains open.
Raw inventory, archives and measurements are retained privately under
artifacts/fair-benchmark-2026-11-05; no original source corpus is published.

## Bounded metadata compression — 0.4.2, 6 November 2026

This isolates one encoder setting, with the same frozen public/synthetic inputs
as the 5 November baseline. The ZIP Deflate Ultra and optimized solid 7z
LZMA2/PPMd measurements below are reused from that unchanged baseline; they
were not rerun. ARCA 0.4.2 was measured today, with creation/integrity together
and extraction separate. All outputs restored exact file paths and SHA-256,
including extraction by the released 0.4.1 reader.

| Dataset | ARCA 0.4.1 B | ARCA 0.4.2 B | Smallest tested 7z B | 0.4.2 vs 7z | Create + verify s | Extract s | Create / extract peak MiB |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| public-text-code | 323,431 | 323,374 | 322,164 | +0.38% | 3.2313 | 0.6671 | 100.3 / 84.4 |
| synthetic-small-files | 26,765 | 25,896 | 22,478 | +15.21% | 1.2404 | 0.9025 | 64.7 / 56.2 |
| incompressible-control | 8,391,558 | 8,391,282 | 8,389,342 | +0.02% | 12.7458 | 0.3772 | 265.4 / 60.0 |

These are single end-to-end observations including process startup, not
confidence intervals or isolated codec throughput. One compression thread;
direct worker benchmark without GUI Job CPU/RAM caps. Peak process working
set includes .NET and is not total committed/virtual memory. Source inventory
and native baseline settings/verification are documented in the previous section.
The initial 0.4.1 creation times were 3.0280, 1.1351 and 12.4734 seconds for
the same three rows; 0.4.2 is not advertised as a creation-speed improvement.

The research probe compressed each exact manifest three times (qualities
6/9/11), verified decoded bytes and unchanged payload segments; quality 6
reproduced the complete original archives. The first call includes cold/JIT
cost. Quality 9 made three of five archives slightly larger and was rejected.
Quality 11 reduced all five, but is slower. Median metadata-only times:

| Fixture | Raw JSON B | Manifest q6 B | Manifest q11 B | q6 ms | q11 ms | q6 / q11 peak MiB |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| public-text-code | 2,709 | 1,126 | 1,069 | 0.70 | 3.58 | 27.2 / 27.3 |
| synthetic-small-files | 28,842 | 6,249 | 5,380 | 1.52 | 33.79 | 26.8 / 26.9 |
| incompressible-control | 5,841 | 2,822 | 2,546 | 1.10 | 7.34 | 42.8 / 43.1 |
| Many.Small.Files | 442,737 | 97,586 | 80,673 | 11.61 | 538.63 | 29.3 / 38.2 |
| Text.Code | 129,989 | 29,786 | 24,487 | 3.83 | 148.96 | 27.9 / 31.3 |

The 2,000-small-file and 600-text-file metadata probes used frozen Balanced
archives; their payloads are untouched research variants, not evidence of a
new Balanced product result. The implementation applies the extra trial only
to Smaller and caps the input at 512 KiB. It still tries quality 6 and retains
it if quality 11 is not smaller. Larger manifests keep quality 6. This bound
limits extra work, without promising a fixed latency on every computer.
The wider matrix with edited Office/PDF documents and repeated end-to-end
timings remains open. All fixtures are public or synthetic; none are personal.

## Compact profile — 7 November 2026, 0.5.0

Four frozen public/synthetic fixtures, one end-to-end observation per profile.
Creation includes process start and built-in decoded verification. Extraction
is separate, followed by identical external SHA-256/path comparisons. All four
Compact archives also restored exactly in released 0.4.2; an encrypted Compact
case did too. One compression thread, direct workers without GUI Job caps.
Peak working set includes runtime and is not committed/virtual memory.

| Dataset | Input B | Smaller 0.4.2 B | Compact B | Smaller / Compact create+verify s | Smaller / Compact extract s | Smaller / Compact create peak MiB | Smaller / Compact extract peak MiB |
| --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| public-text-code | 1,229,584 | 323,374 | 323,374 | 3.2382 / 1.2380 | 0.7423 / 0.6840 | 99.4 / 100.5 | 84.4 / 84.3 |
| synthetic-small-files | 42,258 | 25,896 | 26,780 | 1.2668 / 1.5540 | 1.0144 / 1.0007 | 65.1 / 67.1 | 53.9 / 57.8 |
| public-spreadsheet | 1,029,744 | 50,926 | 50,926 | 2.9369 / 0.8441 | 0.4201 / 0.4387 | 105.8 / 79.1 | 54.9 / 55.1 |
| already-compressed | 785,443 | 670,413 | 670,413 | 2.6148 / 0.8088 | 0.4283 / 0.4140 | 82.2 / 82.2 | 56.4 / 56.3 |

Compact saved creation time on three larger fixtures, with the same bytes.
The small-file fixture was 3.41% larger and 22.67% slower in this observation;
process startup and per-file work matter at this scale. Extraction and memory
are not promised to improve. This is not a statistical throughput ranking.
Smaller output for public text/code is byte-identical between 0.4.2 and 0.5.0.

The codec-cost probe on exact released 0.4.2 segment bytes found Brotli quality
11 took 1.78–2.38 seconds on the spreadsheet, compressed and text fixtures,
while ultra XZ took 0.09–0.24 and PPMd 0.13–0.29 seconds. Brotli11 was not
the size winner in those three probes. This motivated an explicit alternative,
not removal of the strongest search. Small manifest compression remains bounded.

Same-input native controls: ZIP Deflate ultra, solid 7z LZMA2 ultra/64 MiB,
and solid 7z PPMd ultra, one compression thread, one integrity pass and a
separate extraction with exact SHA-256. Text/small-file native baselines are
reused from 5 November; spreadsheet/compressed controls were run today.

| Dataset | ZIP B | 7z LZMA2 B | 7z PPMd B | Compact B |
| --- | ---: | ---: | ---: | ---: |
| public-text-code | 435,772 | 383,285 | 322,164 | 323,374 |
| synthetic-small-files | 51,052 | 22,478 | 22,833 | 26,780 |
| public-spreadsheet | 176,971 | 50,597 | 182,329 | 50,926 |
| already-compressed | 674,038 | 669,924 | 692,926 | 670,413 |

ARCA is larger than the smallest tested 7z in every row. The old Canterbury
.xls is not representative of modern .xlsx. ZIP/PNG are already-compressed
controls, not a representative edited Office/PDF corpus. Modern documents,
repeat end-to-end timings and adaptive trial skipping remain future work.
There are no personal documents in this evidence.

</div>
