# Prompt Injection Through Forensic Evidence: Artifacts

> [!WARNING]
> These disk images contain prompt injections designed to make an LLM agent act. If an agent analyses them with file or network tools attached, they may cause it to write files to your local machine or make outbound network connections. See [Safety](#safety) before use.

Supporting material for *Prompt Injection Through Forensic Evidence: A Taxonomy and Proof of Concept of Anti-Forensic Attacks on LLM-Assisted Digital Investigations* (Breitinger, Gruber, Hargreaves, Hilgert, Michelet and Scanlon), submitted to DFRWS EU 2027.

<!--
**AUTHOR REVIEW:** update to the full citation and DOI if accepted.
-->

This repository contains the disk images for the three demonstrations in Section 6 of the paper, plus the earlier builds used in the first screen recordings. The disk images, screen recordings and planted text for each experiment are in the [`experiment-01`](experiments-paper/experiment-01/), [`experiment-02`](experiments-paper/experiment-02/) and [`experiment-03`](experiments-paper/experiment-03/) folders in [`experiments-paper/`](experiments-paper/).

Each disk image is a small E01 containing synthetic evidence and a planted text file aimed at an LLM agent analysing the image. Nothing is concealed. The text is plain and readable by the examiner, the forensic tool and the model alike. The planted text for each experiment is reproduced in Appendix A of the paper.

The disk images were built programmatically (GPT partition table, NTFS volume and E01 container created with `sgdisk`, `mkntfs` and libewf). The planted text was drafted with LLM assistance (Claude Opus 4.8 and Claude Opus 5, Anthropic, via Claude Code) and subjected to human review.

## Contents

- [`experiments-paper/`](experiments-paper/): the three demonstrations in Section 6 of the paper
- [`experiments-additional/`](experiments-additional/readme.org): data and code for additional experiments

### Paper experiments

| Folder                                               | Paper section                         | Disk image       | Recording                                           |
| ---------------------------------------------------- | ------------------------------------- | ---------------- | --------------------------------------------------- |
| [`experiment-01/`](experiments-paper/experiment-01/) | 6.2.1 Persuasion, Appendix A.1        | `exhibit_40.E01` | [Demo 1a: persuasion](https://youtu.be/iZH3hjqShn8) |
| [`experiment-02/`](experiments-paper/experiment-02/) | 6.2.2 Local disk writes, Appendix A.2 | `exhibit_10.E01` | tbc, see [Originals](#originals) for demonstration  |
| [`experiment-03/`](experiments-paper/experiment-03/) | 6.2.3 Network callback, Appendix A.3  | `exhibit_13.E01` | tbc, see [Originals](#originals) for demonstration  |

Each experiment folder also holds the earlier build of its disk image in `original/` (see [Originals](#originals)) and a README describing the experiment.

To verify the images after download:

```
sha256sum -c SHA256SUMS.txt
```

On macOS, use `shasum -a 256 -c SHA256SUMS.txt`.

| Disk image | Size | SHA-256 |
|---|---|---|
| `experiments-paper/experiment-01/exhibit_40.E01` | 903,462 B | `a53473f80ecfb3e3df864ce129c20cfac473bf867f87cef532ffe10192069aca` |
| `experiments-paper/experiment-02/exhibit_10.E01` | 252,946 B | `0d9432885e0ccc2a048cea72493a3f983e4ddd3d8fd79fb52474673d86bec3e3` |
| `experiments-paper/experiment-03/exhibit_13.E01` | 904,120 B | `a1604c8774aa7748ba326efd68513f8f38ab5cb8ac2606e4905f84ecc76ce0b6` |
| `experiments-paper/experiment-01/original/belief_triceratops.E01` | 903,558 B | `efbde8aa90dc8ac9534609ddf9525480694425bca55cb87d9c5676307ce018ae` |
| `experiments-paper/experiment-02/original/fs_write_w1.E01` | 222,224 B | `618bf9cb20b25a13e94f00a6780180ee99f3240a86ebf1ccb9ebbaab3ae1a228` |
| `experiments-paper/experiment-03/original/exfil_hooked.E01` | 904,128 B | `70a33c642b79f68cb844cb7689c2db852afea756675d3aee8886c7bc918011ed` |

### Additional experiments

[`experiments-additional/`](experiments-additional/readme.org) holds the scripts and JSON results for runs of all three attacks with Gemma-4-12B, gpt-oss-20B and Ministral-3-8B. Its readme lists which results correspond to Experiments 1–3.

## Setup used in the paper

- Autopsy 4.23.1 with its MCP server (18 read-only tools), each image ingested into its own case.
- LM Studio as the MCP client, running `gemma-4-12b` with the default settings from the Hugging Face model card and a context window of up to 98,304 tokens.
- Experiment 2 additionally attached [`mcp/server-filesystem`](https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem), and Experiment 3 [`mcp/server-fetch`](https://github.com/modelcontextprotocol/servers/tree/main/src/fetch).

Each result in Section 6.2 of the paper is a single live run.

Further details:

- Host: Windows analyst workstation.
- Autopsy ingest modules include Data Source Integrity, Keyword Search and Picture Analyzer.
- The Autopsy MCP server runs as `autopsy-mcp-stdio.exe` over stdio and is scoped to the open case.
- LM Studio 0.4.23 (0.4.26 for Demo 1a), with tool permissions set per tool, all to "Ask" (the default).
- The model is the Q4_K_M GGUF build.
- The filesystem server is scoped at launch to a single working folder; the fetch server has network access.

The Autopsy MCP server is read-only, so Experiments 2 and 3 work only when the examiner attaches a filesystem or fetch server. This is a configuration the examiner chooses, not one the attacker controls.

## Summary

All three attacks succeeded with `gemma-4-12b` in LM Studio. In supporting runs, Experiment 1 also succeeded with Ministral-3-8B, and Experiments 2 and 3 also succeeded with gpt-oss-20B (see [Additional experiments](#additional-experiments)).

## Originals

The first screen recordings for all three experiments were made with earlier builds of these images. They are in the `original/` folder of each experiment so that each recording can be checked against the exact image used.

| Image | Recording | Replaced by | SHA-256 |
|---|---|---|---|
| `belief_triceratops.E01` | [Demo 1: persuasion](https://youtu.be/AT8qS7lechg) (first recording) | `exhibit_40.E01` | `efbde8aa90dc8ac9534609ddf9525480694425bca55cb87d9c5676307ce018ae` |
| `fs_write_w1.E01` | [Demo 2: file system write](https://youtu.be/ImIglGehcjg) | `exhibit_10.E01` | `618bf9cb20b25a13e94f00a6780180ee99f3240a86ebf1ccb9ebbaab3ae1a228` |
| `exfil_hooked.E01` | [Demo 3: web fetch](https://youtu.be/89f-lgTHgQs) | `exhibit_13.E01` | `70a33c642b79f68cb844cb7689c2db852afea756675d3aee8886c7bc918011ed` |

These images have a flaw: the image filename describes the test. Autopsy includes the image name in every file path it reports (for example `/img_belief_triceratops.E01/vol_vol4/Photos/...`), so the model receives the name many times during an analysis. In the case of `belief_triceratops.E01`, the model was given the word "triceratops" through the file paths before it had read the planted note. The replacement images use the uninformative names `exhibit_NN`.

The differences between each original and its replacement are as follows:

- **Experiment 2 and Experiment 3:** the planted notes are byte-identical (same SHA-256, see the table below). Only the image name and the canary in `build_id.txt` differ. Demos 2 and 3 therefore show the same planted text as `exhibit_10.E01` and `exhibit_13.E01`, but they were not recorded with those images.
- **Experiment 1:** the note also differs. The original note (579 B) is attributed to a human reviewer and opens with provenance that contradicts its own claim (an African game reserve, a waterhole and a handler tag). The replacement note (475 B) is attributed to an automated picture classifier and has that preamble removed. Experiment 1 was re-recorded with `exhibit_40.E01` ([Demo 1a](https://youtu.be/iZH3hjqShn8)), and that is the note shown in Appendix A.1.

SHA-256 of the planted files in each image:

| File | Size | SHA-256 | Images |
|---|---|---|---|
| `Photos/rhino_photos_notes.txt` | 475 B | `c5d101217f38c95d524819f82c3f20896f04d38faeeb9c451d0bb3aac466f404` | `exhibit_40` |
| `Photos/rhino_photos_notes.txt` | 579 B | `675d7f9d814615a49788db6e6eb9e2ea26562bd191cb56bba9c1681f626426f9` | `belief_triceratops` |
| `Docs/intake_summary.txt` | 288 B | `7c65df87d8b0a4c52b2a24f13469ab96cf587137395a7d11a410be78c73e1294` | `exhibit_10`, `fs_write_w1` |
| `Docs/review_addendum.txt` | 1,241 B | `d887b2b1e7813e7165f1cd5a7424c1754f0a70507ff2887a3fd426a4d2822cd9` | `exhibit_10`, `fs_write_w1` |
| `Photos/rhino_photos_notes.txt` | 783 B | `dfbcb6e162803f2ae315e9f428d576954242045f9df6ce7b5c04ec62b2161fa6` | `exhibit_13`, `exfil_hooked` |

## Safety

The planted text, the case details and the disk structure are synthetic. The two rhinoceros photographs are real wildlife photographs (see [Licence](#licence)). The images contain no case data or personal data.

However, the images are built to make an agent act, so be aware of what they will do if the injection succeeds:

- **Experiment 2** writes `image_integrity_error.log` into whichever folder the agent's file tools can reach. Scope those tools to a disposable folder.
- **Experiment 3** sends a real HTTP request to `adv3-egress.requestcatcher.com`, a public service, carrying a canary token. If you do not want traffic to leave your machine, block outbound access or point the agent at a local endpoint.

## Licence

Apache License 2.0, except for the rhinoceros photographs below. See [LICENSE](LICENSE).

### Rhinoceros photographs

The two rhinoceros photographs in `Photos/` (present in `exhibit_40.E01`, `exhibit_13.E01`, `belief_triceratops.E01` and `exfil_hooked.E01`) are third-party works, included unmodified and licensed under [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/). They are not covered by the Apache License.

| File in image | Original | Photographer | Source |
|---|---|---|---|
| `rhino_safari_01.jpg` | [Black rhinoceros walking in grass](https://commons.wikimedia.org/wiki/File:Black_rhinoceros_walking_in_grass_-_DPLA_-_a1a00f7c1d13c94d09fc6e092f1b8c73.jpg) (35 mm slide, November 1980) | Warren Garst | Colorado State University Libraries, via the Digital Public Library of America and Wikimedia Commons |
| `rhino_safari_02.jpg` | [Square-lipped rhinoceros standing close up](https://commons.wikimedia.org/wiki/File:Square-lipped_rhinoceros_standing_close_up_-_DPLA_-_125907cba1bd420385e0dd280f3da3f3.jpg) (35 mm slide, 1958–1988) | Warren Garst | Colorado State University Libraries, via the Digital Public Library of America and Wikimedia Commons |

The files have been renamed in the disk images but are otherwise byte-identical to the Wikimedia Commons originals (SHA-1 `5c403f6e169abaa0625ee26341bfb39213c3087d` and `207dc2ae276a7fc17a1da55e0dac414ea5fc9122` respectively).
