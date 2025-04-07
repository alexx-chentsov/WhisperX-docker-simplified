# Guide how to transcribe video or audio into desired language (with speaker diarization)

Before: a video with audio in many languages

After: a video with audio in many languages with transcript in one desired language (e.g., Eng) and this transcript is synced with video playing if VLC player is used

This "program" will also translate other languages to the target one (e.g., if model is large-v3-en and someone speak Russian, it will translate Russian to English and the result transcript will be in English)

For macos users, edit and then run the following command:
0. You do NOT need to clone this repo
1. Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
2. Open Docker Desktop -> settings -> resources -> set memory limit to 16Gb and swap to 4Gb, just to make sure it'll work. If you don't have so much memory, try with other settings but you'll probably see "killed" in the output because of [OOM](https://en.wikipedia.org/wiki/Out_of_memory)
3. Create a new temporary directory and copy your video to it
4. Within the new directory, run the following script, but before running update it:
   - update "some_video_in_russian.mp4" with your file name (quotes are needed at least for filename that contains spaces)
   - set "YOUR_HF_TOKEN" with your Hugging Face token
   - if there are more or less than 2 speakers, change "--min_speakers X --max_speakers X" where X is the number of speakers
  ```bash
  sudo chmod -R 777 . && docker run -it -v ".:/app" ghcr.io/jim60105/whisperx:large-v3-en -- --output_format srt "some_video_in_russian.mp4" --diarize --min_speakers 1 --max_speakers 1 --compute_type int8 --hf_token YOUR_HF_TOKEN
  ```
5. You should see something like:
  ```bash
  INFO:speechbrain.utils.quirks:Applied quirks (see `speechbrain.utils.quirks`): [allow_tf32, disable_jit_profiling]
  INFO:speechbrain.utils.quirks:Excluded quirks specified by the `SB_DISABLE_QUIRKS` environment (comma-separated list): []
  >>Performing voice activity detection using Pyannote...
  Lightning automatically upgraded your loaded checkpoint from v1.5.4 to v2.5.0.post0. To apply the upgrade to your files permanently, run `python -m pytorch_lightning.utilities.upgrade_checkpoint ../venv/lib/python3.11/site-packages/whisperx/assets/pytorch_model.bin`
  Model was trained with pyannote.audio 0.0.1, yours is 3.3.2. Bad things might happen unless you revert pyannote.audio to 0.x.
  Model was trained with torch 1.10.0+cu102, yours is 2.5.1. Bad things might happen unless you revert torch to 1.x.
  >>Performing transcription...
  Transcript: [0.031 --> 20.197]  something
  Transcript: [20.888 --> 38.877]  something
  >>Performing alignment...
  >>Performing diarization...
  config.yaml: 100%|████████████████████████████████████████████████████████████████████████████████████████████████| 469/469 [00:00<00:00, 1.85MB/s]
  pytorch_model.bin: 100%|██████████████████████████████████████████████████████████████████████████████████████| 5.91M/5.91M [00:00<00:00, 14.6MB/s]
  config.yaml: 100%|████████████████████████████████████████████████████████████████████████████████████████████████| 399/399 [00:00<00:00, 2.83MB/s]
  pytorch_model.bin: 100%|██████████████████████████████████████████████████████████████████████████████████████| 26.6M/26.6M [00:01<00:00, 25.2MB/s]
  ```
6. As a result, in the dir, you'll find "some_video_in_russian.srt" - a new file with transcript
7. Now, just open your video with [VLC](https://www.videolan.org/) and it will automatically discover this transcript and show it during the video playing

# Notes
- To translate transcript to Russian, use `large-v3-ru` model instead. For other languages, research the docs listed in Sources
- To run using GPU (e.g., for Windows users), add `--gpus all` after `docker run` command
- For macos, "--compute_type int8" is required, see https://github.com/m-bain/whisperX docs
- Feel free to fix or extend this guide with your suggestions

# Some dev things you should not care about

Set up
```
# 1. get hf token

# 2. get hf permissions for
#   https://huggingface.co/pyannote/segmentation-3.0
#   https://huggingface.co/pyannote/speaker-diarization-3.1
```

Sources
- https://github.com/m-bain/whisperX
- https://github.com/jim60105/docker-whisperX
