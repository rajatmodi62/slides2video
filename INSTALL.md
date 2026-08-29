# Installation

Turns a `.pptx` (with speaker notes) into a narrated `.mp4`. Each slide holds
on screen exactly as long as its notes take to speak.

## System dependencies

```bash
sudo apt-get update
sudo apt-get install -y libreoffice poppler-utils ffmpeg
```

- `libreoffice` — converts `.pptx` -> `.pdf`
- `poppler-utils` (`pdftoppm`) — converts `.pdf` pages -> `.png` slide images
- `ffmpeg` — renders per-slide video clips and concatenates final `.mp4`

GPU (CUDA) recommended for TTS generation speed, not required.

## Python environment

Python 3.10-3.13.

```bash
conda create -yn slides2video python=3.11
conda activate slides2video
```

## Install chatterbox-tts (from source, included in this repo)

```bash
cd chatterbox
pip install -e .
cd ..
```

This pulls in torch, torchaudio, transformers, and the rest of chatterbox's
pinned deps from `chatterbox/pyproject.toml`.

## Install remaining python deps

```bash
pip install python-pptx
```

(`torch` / `torchaudio` already come in via the chatterbox install above.)

## Usage

Make a `.pptx`, add speaker notes to each slide, then:

```bash
python slides_to_video.py my_deck.pptx --out my_deck.mp4
```

Optional flags:

```bash
python slides_to_video.py my_deck.pptx \
  --out my_deck.mp4 \
  --voice my_voice_sample.wav \   # 5-10s reference clip to clone a voice
  --workdir work \                # intermediate files (slide images/audio/clips)
  --dpi 150                       # slide render resolution
```

First run downloads the Chatterbox model weights from Hugging Face
(`ChatterboxTTS.from_pretrained(device="cuda")`) — needs internet access and
a few GB of disk/VRAM.

## Troubleshooting

- **`libreoffice`/`pdftoppm` not found** — install the system deps above.
- **Image/notes count mismatch warning** — check for hidden slides in the
  `.pptx`; the script prints a warning but continues using `min(images, notes)`.
- **No CUDA available** — edit `slides_to_video.py` and change
  `device="cuda"` to `device="cpu"` (slower) or `device="mps"` on Mac.
