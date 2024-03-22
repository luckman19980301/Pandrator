<p align="left">
  <img src="pandrator.png" alt="Icon" width="200" height="200"/>
</p>

# Pandrator, an audiobook generator

Pandrator is a tool designed to transform text into spoken audio in multiple languages using a variety of APIs and processing techniques. 
It is still in alpha stage and I'm not an experience developer (I'm a noob, in fact), so the code is far from perfect in terms of optimisation, features and reliability. Please keep this in mind.
It leverages the XTTS model(s) for text-to-speech conversion, enhanced by RVC_CLI for quality improvement and better voice cloning results, and NISQA for audio quality evaluation. Additionally, it incorporates Text Generation Webui's API for local LLM-based text pre-processing, enabling a wide range of text manipulations before audio generation.

- [Pandrator, an audiobook generator](#pandrator-an-audiobook-generator)
  - [Requirments](#requirments)
    - [Hardware](#hardware)
    - [Dependencies](#dependencies)
      - [Required](#required)
      - [Optional](#optional)
  - [Installation](#installation)
    - [Minimal One-Click Installation Executable (Windows with an Nvidia GPU only)](#minimal-one-click-installation-executable-windows-with-an-nvidia-gpu-only)
    - [Manual Installation](#manual-installation)
  - [Features](#features)
  - [Quick Start Guide](#quick-start-guide)
    - [Basic Usage](#basic-usage)
    - [General Audio Settings](#general-audio-settings)
    - [General Text Pre-Processing Settings](#general-text-pre-processing-settings)
    - [LLM Pre-processing](#llm-preprocessing)
    - [RVC Quality Enhancement and Voice Cloning](#rvc-quality-enhancement-and-voice-cloning)
    - [NISQA TTS Evaluation](#nisqa-tts-evaluation)
  - [Contributing](#contributing)
  - [Tips](#tips)
  - [To-do](#to-do)

![UI Demonstration Image](ui_demonstration.png)

## Requirments

### Hardware
I was able to run all functionalities on a laptop with a Ryzen 5600h and a 3050 laptop GPU (4GB of VRAM). It's likely that you will need at least 16GB of RAM, a reasonably modern CPU, and ideally an NVIDIA GPU with 4 GB+ of VRAM for usable performance. Consult the requirments of the services listed below.

### Dependencies
This project relies on several APIs and services (running locally):

#### Required
- [XTTS API Server by daswer123](https://github.com/daswer123/xtts-api-server.git) for Text-to-Speech (TTS) generation using Coqui [XTTSv2](https://huggingface.co/coqui/XTTS-v2)
- [FFmpeg](https://github.com/FFmpeg/FFmpeg) for audio encoding

#### Optional
- [Text Generation Webui API by oobabooga](https://github.com/oobabooga/text-generation-webui.git) for LLM-based text pre-processing
- [RVC_CLI by blaise-tk](https://github.com/blaise-tk/RVC_CLI.git) for enhancing voice quality and cloning results with [Retrieval Based Voice Conversion](https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI)
- [NISQA by gabrielmittag](https://github.com/gabrielmittag/NISQA.git) for evaluating TTS generations (using the [FastAPI implementation](https://github.com/lukaszliniewicz/NISQA-API))

## Installation

### Minimal One-Click Installation Executable (Windows with an Nvidia GPU only):
Run `pandrator_start_minimal.exe` with administrator priviliges. The executable was created using [pyinstaller](https://github.com/pyinstaller/pyinstaller) from `pandrator_start_minimal.py` in the repository.

**It may be flagged as a threat by antivirus software, so you may have to add it as an exception.**

On first use it creates the Pandrator folder, installs `curl`, `git`, `ffmpeg` and `Miniconda`, clones the XTTS Api Server repository and the Pandrator repository, creates conda environments, installs dependencies and launches them. You may use it to launch Pandrator later. If you want to perform the setup again, remove the Pandrator folder it created. Please allow at least a couple of minutes for the initial setup process to download models and install dependencies (it takes about 7 minutes for me).

For additional functionality:
- Install Text Generation Webui and remember to enable the API (add `--api` to `CMD_FLAGS.txt` in the main directory of the Webui before starting it).
- Set up RVC_CLI for enhancing generations with RVC.
- Set up NISQA API for automatic evaluation of generations.
Please refer to the repositories linked above for detailed installation instructions. Remember that the APIs must be running to make use of the functionalities they offer.

### Manual Installation:
1. Make sure that Python 3 is installed.
2. Install and run at last XTTS API Server. 
3. Clone this repository.
4. `cd` to the repository directory.
5. Install requirements using `pip install -r requirements.txt`.
6. Run `python pandrator.py`.

## Features
- **Text Pre-processing:** Splits text into sentences and (attempts to) preserve paragraphs. Profiles for multiple languages are available.
- **LLM Text Pre-processing:** Utilizes a local LLM for text corrections and enhancements with up to three different prompts run sequentially, and an evaluation mechanism that asks the model to perform a task twice and then choose the better response. I've been using `openchat-3.5-0106.Q5_K_M.gguf` with good results, as well as for example `Mistral 7B Instruct 0.2`. Different models may perform different tasks well, so it's possible to choose a specific model for a specific prompt.
- **Audio Generation:** Converts processed text into speech, with options for voice cloning and quality enhancement.
- **Audio Evaluation:** An experimental feature that predicts Mean Opinion Score (MOS) for generated sentences and sets a score threshold or chooses the best score from a set number of generations.
- **Session Management:** Supports creating, deleting, and loading sessions for organized workflow.
- **GUI:** Built with customtkinker for a user-friendly experience.

## Quick Start Guide

![Demonstration GIF](demonstration.gif)

### Basic Usage
If you don't want to use the additional functionalities, you have everything you need in the Session tab. 
1. Either create a new session or load an existing one (select a folder in `Outputs` to do that).
2. Choose your `.txt` file.
3. Select a language from the dropdown.
4. Choose the voice you want to use (voices are short, 8-12s `.wav` files in the `tts_voices` directory). The XTTS model uses the audio to clone the voice and use it for generation. You may use the example ones in the repository, which I borrowed from daswer123, or upload your own. Please make sure that the audio is between 8 and 12s, mono, and 22050khz. You may use a tool like Audacity prepare the files. The less noise, the better. You may use a tool like [Resemble AI](https://github.com/resemble-ai/resemble-enhance) for denoising and/or enhancement of your samples on [Hugging Face](https://huggingface.co/spaces/ResembleAI/resemble-enhance). 
5. Start the generation. You may stop and resume it later, or close the programme and load the session later.
6. You can play back the generated sentenced, also as a playlist, and regenerate or remove individual ones.
7. "Save Output" concatenates the sentences generated so far an encodes them as one file (default is `.opus` at 64k bitrate; you may change it in the Audio tab to `.wav` or `.mp3`).

### General Audio Settings
1. You can change the lenght of silence appended at the end of sentences and paragraphs.
2. You can enable a fade-in and -out effect and set the duration.
3. You can choose the output format and bitrate.

### General Text Pre-Processing Settings
1. You can disable/enable splitting long sentences and set the max lenght a text fragment sent for TTS generation may have (enabled by default; it tries to split sentences whose lenght exceeds the max lenght value; it looks for punctuation marks (, ; : -) and chooses the one closest to the midpoint of the sentence.
2. You can disable/enable appending short sentences to preceding or following sentences (disabled by default, may perhaps improve the flow as the lenght of text fragments sent to the TTS API is more uniform).
3. Remove diacritics (useful when generating a text that contains many foreign words or transliterations from foreign alphabets). Do not enable this if you generate in a language that needs diacritics! The pronounciation will be wrong then.

### LLM Pre-processing
- Enable LLM processing to use language models for preprocessing the text before sending it to the TTS API. For example, you may ask the LLM to remove OCR artifacts, spell out abbreviations, correct punctuation etc.
- You can define up to three prompts for text optimization. Each prompt is sent to the LLM API separately, and the output of the last prompt is used for TTS generation.
- For each prompt, you can enable/disable it, set the prompt text, choose the LLM model to use, and enable/disable evaluation (if enabled, the LLM API will be called twice for each prompt, and they again for the model to choose the better result).
- Load the available LLM models using the "Load LLM Models" button in the Session tab.

### RVC Quality Enhancement and Voice Cloning
- Enable RVC (Real-time Voice Cloning) to enhance the generated audio quality and apply voice cloning.
- Select the RVC model file (.pth) and the corresponding index file using the "Select RVC Model" and "Select RVC Index" buttons in the Audio Processing tab.
- When RVC is enabled, the generated audio will be processed using the selected RVC model and index before being saved.

### NISQA TTS Evaluation
- Enable TTS evaluation to assess the quality of the generated audio using the NISQA (Non-Intrusive Speech Quality Assessment) model.
- Set the target MOS (Mean Opinion Score) value and the maximum number of attempts for each sentence.
- When TTS evaluation is enabled, the generated audio will be evaluated using the NISQA model, and the best audio (based on the MOS score) will be chosen for each sentence.
- If the target MOS value is not reached within the maximum number of attempts, the best audio generated so far will be used.

## Contributing
Contributions, suggestions for improvements, and bug reports are welcome. Please refer to the contributing guidelines for more information.

## Tips
- You can find a collection  of voice sample for example [here](https://aiartes.com/voiceai). They are intended for use with ElevenLabs, so you will need to pick an 8-12s fragment and save it as 22050khz mono .wav usuing for example Audacity. 
- You can find a collection of RVC models for example [here](https://voice-models.com/).

## To-do
- [ ] Add the other APIs to the setup script.
- [ ] Add importing/exporting settings.
- [ ] Add support for proprietary APIs for text pre-processing and TTS generation.
- [ ] Enhance file format support (e.g., HTML, XML, PDF, Epub) including direct PDF to TXT conversion with OCR.
- [ ] Integrate editing capabilities for processed sentences within the UI.
- [ ] Add support for a higher quality local TTS model, Tortoise.
- [ ] Add support for a lower quality but faster local TTS model that can easily run on CPU, e.g. Silero or Piper.
- [ ] Implement a better text segmentation method, e.g. NLP-based.
- [ ] Add option to record a voice sample and use it for TTS to the GUI.
