# Upstream Tiny Tapeout templates

This template supports every Tiny Tapeout PDK from one repo: `project.pdk` in `info.yaml` selects it. Tiny Tapeout
keeps one template per PDK. This repo is a Git fork of the SKY130 one (remote `upstream`), and folds in the
PDK-specific parts of the other two, which it does not track by Git. Last synced from:

| Upstream | Commit | Date |
|---|---|---|
| [TinyTapeout/ttsky-verilog-template](https://github.com/TinyTapeout/ttsky-verilog-template) | `a42e666` | 2026-10-14 |
| [TinyTapeout/ttgf-verilog-template](https://github.com/TinyTapeout/ttgf-verilog-template) | `e0d770f` | 2026-09-30 |
| [TinyTapeout/ttihp-verilog-template](https://github.com/TinyTapeout/ttihp-verilog-template) | `4b1204a` | 2026-10-28 |
| [TinyTapeout/tt-gds-action](https://github.com/TinyTapeout/tt-gds-action) (pinned in `.github/workflows`) | `d4783db` | 2026-11-02 |

## The PDK-specific parts

- `.github/workflows/{gds,docs,fpga}.yaml`: one `tt-gds-action` commit for every PDK, instead of TT's per-shuttle
  tags, and `pdk:` read from `info.yaml`. The action picks the LibreLane version from `pdk`, and its precheck,
  gate-level test, and viewer read the PDK from the build.
- `test/Makefile`: the gate-level cell models for each PDK.
- `test/tb.v`: power pins under `USE_POWER_PINS`, which `test/Makefile` sets for the PDKs whose netlists have them.
- `src/config.json`: IHP's power-grid settings, in a `pdk::ihp-sg13*` block.
- `info.yaml`: `pdk`, and the tile-size comment.
- `.devcontainer`: SKY130 only, as in TT's SKY template.

## Updating

1. **SKY130:** `git fetch upstream && git merge upstream/main`. Keep the files above PDK-generic when resolving
   conflicts; a per-shuttle tag bump (`@ttskyNNx`) conflicts with the pinned commit, so keep the commit (step 3).
2. **GF180 and IHP:** list what changed since the commit above, e.g.
   `gh api repos/TinyTapeout/ttgf-verilog-template/compare/e0d770f...main --jq '.files[].filename'`, and port any
   change to the files above (shuttle tag bumps excepted).
3. **tt-gds-action:** pin the newest commit that contains every current shuttle tag (usually `main`): for each
   tag, `gh api repos/TinyTapeout/tt-gds-action/compare/<tag>...<commit> --jq .status` gives `ahead` or
   `identical`. Use the full SHA in all three workflows.
4. Update the table, then run the flow for each PDK (set `project.pdk`) and check that it passes.
