# IMPROVEMENTS.md

*Analysis date: 2026-07-11*

This repository (`sheet-music`) is intended to be a home for Greg's musical compositions, but per the README's own admission ("I have lots of compositions, almost none of which are in here. I plan to fix this one day") it is essentially empty: a single initial commit containing only `README.md` and `LICENSE`. There is one untracked directory, `openai-fugue/`, containing a MusiXTeX experiment (an LLM-prompted 4-part fugue on "Twinkle, Twinkle, Little Star") that has never been committed. The project's biggest problem is not code quality — it's that the actual content it exists to hold isn't in it.

## Bugs & Fixes

- `openai-fugue/fugue.tex` is unfinished and partly broken: `\sop` and `\alt` are defined as empty macros (`\def\sop{%1 }`), so the `\Notes\sop \ql{...}` lines rely on macros that expand to nothing; the tenor and bass parts and all subsequent fugue entries are commented out. Either finish the four-voice engraving (uncomment and define `\ten`/`\bas`, use `\setstaffs1{4}` correctly with `\nextinstrument` per voice) or clearly mark it as an abandoned experiment.
- MusiXTeX requires a three-pass build (`pdflatex` → `musixflx` → `pdflatex`) to fix slurs/spacing; the `Makefile` runs `pdflatex fugue` only once. Add the `musixflx fugue` pass, or the `.mx1` files will never be resolved into correct output.

## Housekeeping

- **Commit the actual work.** `git status` shows `openai-fugue/` entirely untracked since the initial commit. Commit `fugue.tex`, `test.tex`, and the `Makefile` (the sources), and add a `.gitignore` excluding LaTeX build products: `*.aux`, `*.log`, `*.mx1`, `*~`, and optionally the PDFs (or commit the PDFs deliberately as rendered deliverables — pick one policy and state it in the README).
- Delete the editor backup files `fugue.tex~` and `test.tex~`.
- Add a `clean` target to the Makefile, and generalize it (pattern rule `%.pdf: %.tex`) so `test.tex` builds too.

## Improvements

- **Import the compositions.** This is the stated purpose of the repo and the single highest-value action. Decide on a canonical source format — LilyPond (`.ly`) is the strongest candidate for version-controlled sheet music (plain text, diffs well, excellent output); MusiXTeX works but is far more painful to write; MusicXML is good as an interchange/export format. Establish one directory per piece with source + Makefile.
- Add a top-level Makefile or small script that builds every piece's PDF, so the whole repo renders with one command.
- Consider a GitHub Actions workflow (or a cron on raksasa) that builds all PDFs on push and publishes them — e.g. to a static page on merah — so the rendered scores are always browsable without a local TeX install.

## Documentation

- Expand `README.md`: what pieces exist (even as a checklist of compositions to import), what toolchain is needed (`texlive` with `musixtex`, or `lilypond`), and how to build. Right now it is one self-deprecating sentence.
- In `openai-fugue/`, keep the prompt comment (it documents provenance — an OpenAI-generated experiment) but add a short note on its status and what "done" would look like.

## Testing

- Formal tests don't apply, but a CI job that verifies every `.tex`/`.ly` source compiles cleanly (non-zero exit on LaTeX errors — note `pdflatex` needs `-halt-on-error` for that) is the musical equivalent of a build check and worth having once content lands.

## Security

- Nothing sensitive found: no credentials, no scripts, MIT-style LICENSE plus TeX sources only.

## Quick Wins

1. `git add openai-fugue/*.tex openai-fugue/Makefile` + a `.gitignore` for TeX detritus — five minutes, and the repo stops being empty.
2. Add `musixflx` pass and `clean:` target to the Makefile.
3. Remove `*~` backup files.
4. Write the "pieces to import" checklist into the README so the repo's backlog is visible.
