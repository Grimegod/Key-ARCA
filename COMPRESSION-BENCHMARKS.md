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
