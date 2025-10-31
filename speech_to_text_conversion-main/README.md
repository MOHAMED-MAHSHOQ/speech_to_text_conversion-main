# Speech to Text Conversion — What I actually did

This repository contains a single Jupyter notebook (`speech_to_text.ipynb`) that implements an experiment to transcribe audio using a small preprocessing pipeline and OpenAI Whisper.

Below I describe exactly what I did, which libraries the notebook uses, how to run it (Colab and locally), and recommended next steps.

## Exactly what I did in the notebook

1. Installed runtime packages inside the notebook using pip (the notebook includes an install cell).
2. Uploaded an audio file using `google.colab.files.upload()` (the notebook was developed with Colab in mind).
3. Converted the uploaded audio to a WAV file using `pydub.AudioSegment` and standardized it to 16 kHz, mono, 16-bit sample width.
4. Read the WAV file with `scipy.io.wavfile` and converted the data to float32 / normalized form.
5. Applied noise reduction using `noisereduce.reduce_noise` and saved the cleaned audio as `cleaned.wav`.
6. Loaded the Whisper model (`medium`) via the `whisper` package and transcribed the cleaned audio using `model.transcribe(cleaned_path)`.
7. Printed (and saved) the resulting transcript text.

## Exact libraries and tools the notebook uses

- openai-whisper (`whisper`) — loads Whisper models and performs transcription. The notebook uses the `medium` model.
- noisereduce — performs noise reduction on waveform arrays.
- pydub — reading and converting many audio formats, exporting WAV. Requires `ffmpeg` installed on the system.
- ffmpeg / ffmpeg-python — helper bindings and ffmpeg dependency for pydub.
- scipy (scipy.io.wavfile) — read/write WAV files.
- numpy — numeric array processing.
- google.colab.files — for file upload convenience in Google Colab (not available outside Colab).

The notebook includes this pip install line near the top:

```
!pip install -q openai-whisper noisereduce pydub ffmpeg-python
```

## How to run

Option A — Quick start in Google Colab (recommended for first run):

1. Upload the notebook to Colab (File -> Upload notebook) or open it directly from Google Drive.
2. Run the cells top-to-bottom. The `google.colab.files.upload()` cell will prompt you to select your audio file.
3. The notebook will install dependencies at runtime and write `converted.wav` and `cleaned.wav` into the Colab filesystem. The final cell runs Whisper and prints the transcript.

Option B — Run locally (Windows, cmd.exe):

1. Install ffmpeg for Windows and ensure `ffmpeg.exe` is on your PATH. pydub requires ffmpeg.
2. Create and activate a virtual environment (Windows cmd):

```cmd
python -m venv .venv
.venv\Scripts\activate
```

3. Install the packages used in the notebook:

```cmd
pip install jupyter openai-whisper noisereduce pydub ffmpeg-python numpy scipy
```

4. Open the notebook locally:

```cmd
jupyter notebook speech_to_text.ipynb
```

5. Replace the Colab uploader cell with a local assignment, for example:

```python
input_filename = "data/example_audio.mp3"  # set to your local file
```

6. Run the notebook cells in order. The notebook will produce `converted.wav` and `cleaned.wav`, and then run Whisper to create the transcript.

## Important caveats and notes

- Whisper `medium` is large and may be slow on CPU-only machines. Use `small` or `base` if you want faster runs with lower resource needs.
- The notebook normalizes audio to float32 before noise reduction; beware of scaling issues if you change sample widths or file types.
- `google.colab.files.upload()` is a Colab convenience — replace it for local usage as shown above.
- If you plan to run many transcriptions, consider extracting the core logic into a script that accepts file paths and a model name rather than using the notebook interactively.

## Files in this repo

- `speech_to_text.ipynb` — Jupyter notebook implementing the pipeline described above.
- `README.md` — this file, describing exactly what I did and how to run it.

## Recommended next steps (I can implement any of these)

1. Add a `requirements.txt` that pins the package versions used in the notebook.
2. Add a `data/` folder with one small example audio file and update the notebook to use it by default.
3. Extract the notebook code into a small CLI script (`run_transcription.py`) so the pipeline can be run non-interactively and added to CI.
4. Add a simple smoke test (run pipeline on the small example audio and assert transcript is non-empty).

If you want, tell me which follow-up to implement and I will do it next (I can generate `requirements.txt` and modify the notebook to run locally, or extract a script and add a test).
