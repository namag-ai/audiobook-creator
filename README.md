# Audiobook Creator

## Overview

Audiobook Creator is an open-source project designed to convert books in various text formats (e.g., EPUB, PDF, etc.) into fully voiced audiobooks with intelligent character voice attribution. It leverages modern Natural Language Processing (NLP), Large Language Models (LLMs), and Text-to-Speech (TTS) technologies to create an engaging and dynamic audiobook experience. The project is licensed under the GNU General Public License v3.0 (GPL-3.0), ensuring that it remains free and open for everyone to use, modify, and distribute.


<details>
  <summary>The project consists of three main components:</summary>

1. **Text Cleaning and Formatting (`book_to_txt.py`)**:
   - Extracts and cleans text from a book file (e.g., `book.epub`).
   - Normalizes special characters, fixes line breaks, and corrects formatting issues such as unterminated quotes or incomplete lines.
   - Extracts the main content between specified markers (e.g., "PROLOGUE" and "ABOUT THE AUTHOR").
   - Outputs the cleaned text to `converted_book.txt`.

2. **Character Identification and Metadata Generation (`identify_characters_and_output_book_to_jsonl.py`)**:
   - Identifies characters in the text using Named Entity Recognition (NER) with the GLiNER model.
   - Assigns gender and age scores to characters using an LLM via an OpenAI-compatible API.
   - Outputs two files:
     - `speaker_attributed_book.jsonl`: Each line of text annotated with the identified speaker.
     - `character_gender_map.json`: Metadata about characters, including name, age, gender, and gender score.

3. **Audiobook Generation (`generate_audiobook.py`)**:
   - Converts the cleaned text (`converted_book.txt`) or speaker-attributed text (`speaker_attributed_book.jsonl`) into an audiobook using the Kokoro TTS model ([Hexgrad/Kokoro-82M](https://huggingface.co/hexgrad/Kokoro-82M)).
   - Offers two narration modes:
     - **Single-Voice**: Uses a single voice for narration and another voice for dialogues for the entire book.
     - **Multi-Voice**: Assigns different voices to characters based on their gender scores.
   - Saves the audiobook in the selected output format to `generated_audiobooks/audiobook.{output_format}`.
</details>

## Key Features

- **Gradio UI App**: Create audiobooks easily with an easy to use, intuitive UI made with Gradio.
- **M4B Audiobook Creation**: Creates compatible audiobooks with covers, metadata, chapter timestamps etc. in M4B format.
- **Multi-Format Input Support**: Converts books from various formats (EPUB, PDF, etc.) into plain text.
- **Multi-Format Output Support**: Supports various output formats: AAC, M4A, MP3, WAV, OPUS, FLAC, PCM, M4B.
- **Text Cleaning**: Ensures the book text is well-formatted and readable.
- **Character Identification**: Identifies characters and infers their attributes (gender, age) using advanced NLP techniques.
- **Customizable Audiobook Narration**: Supports single-voice or multi-voice narration for enhanced listening experiences.
- **Progress Tracking**: Includes progress bars and execution time measurements for efficient monitoring.
- **Open Source**: Licensed under GPL v3.

## Sample Text and Audio

<details>
  <summary>Expand</summary>

- `sample_book_and_audio/The Adventure of the Lost Treasure - Prakhar Sharma.epub`: A sample short story in epub format as a starting point.
- `sample_book_and_audio/The Adventure of the Lost Treasure - Prakhar Sharma.pdf`: A sample short story in pdf format as a starting point.
- `sample_book_and_audio/The Adventure of the Lost Treasure - Prakhar Sharma.txt`: A sample short story in txt format as a starting point.
- `sample_book_and_audio/converted_book.txt`: The cleaned output after text processing.
- `sample_book_and_audio/speaker_attributed_book.jsonl`: The generated speaker-attributed JSONL file.
- `sample_book_and_audio/character_gender_map.json`: The generated character metadata.
- `sample_book_and_audio/sample_multi_voice_audiobook.m4b`: The generated sample multi-voice audiobook in M4B format with cover and chapters from the story.
- `sample_book_and_audio/sample_multi_voice_audio.mp3`: The generated sample multi-voice MP3 audio file from the story.
- `sample_book_and_audio/sample_single_voice_audio.mp3`: The generated sample single-voice MP3 audio file from the story.
</details>

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/namag-ai/audiobook-creator.git
   cd audiobook-creator
   ```
2. Create a virtual environment with Python 3.12:
   ```bash
   virtualenv --python="python3.12" .venv
   ```
3. Activate the virtual environment:
   ```bash
   source .venv/bin/activate
   ```
4. Install Pip 24.0:
   ```bash
   pip install pip==24.0
   ```
5. Install dependencies (choose CPU or GPU version):
   ```bash
   pip install -r requirements_cpu.txt
   ```
   ```bash
   pip install -r requirements_gpu.txt
   ```
6. Upgrade version of six to avoid errors:
   ```bash
   pip install --upgrade six==1.17.0
   ```
7. Install [calibre](https://calibre-ebook.com/download) (Optional dependency, needed if you need better text decoding capabilities, wider compatibility and want to create M4B audiobook). Also make sure that calibre is present in your PATH. For MacOS, do the following to add it to the PATH:
   ```bash
   echo 'export PATH="/Applications/calibre.app/Contents/MacOS:$PATH"' >> ~/.zshrc
   source ~/.zshrc  # Apply changes
   ```
8. Install [ffmpeg](https://www.ffmpeg.org/download.html) (Needed for audio output format conversion and if you want to create M4B audiobook)
9. Set up your LLM and expose an OpenAI-compatible endpoint (e.g., using LM Studio with `qwen2.5-14b-instruct-mlx`).
10. Set up the Kokoro TTS model. Use CUDA-based GPU inference for faster processing or use CPU inference via [Kokoro-FastAPI](https://github.com/remsky/Kokoro-FastAPI). Checkout to the commit id `b00c9ec28df0fd551ae25108a986e04d29a54f2e` as latest versions dont support AAC output. Use the following command inside the repo once you've cloned it and run through the dockerfile:
   ```bash
   git checkout b00c9ec28df0fd551ae25108a986e04d29a54f2e
   ```
11. Create a .env file from .env_sample and configure it with correct values
   ```bash
   cp .env_sample .env
   ```

## Usage

<details>
  <summary>Run through Gradio UI (recommended)</summary>

   1. Activate the virtual environment.
   2. Make sure your .env is correctly configured using .env_sample
   3. Run `uvicorn app:app --host 0.0.0.0 --port 7860` to run the Gradio app. After the app has started, navigate to `http://127.0.0.1:7860` in the browser.
</details>

<details>
  <summary>Run through scripts</summary>

   1. Activate the virtual environment.
   2. Make sure your .env is correctly configured using .env_sample
   3. Run `python book_to_txt.py {book_path}` to clean and format the book text. You can give the path to the book in the arguments to the python command or as an input. Also, after conversion you can manually edit the converted book for fine-grained control.
   4. *(Optional for multi-voice narration)* Run `python identify_characters_and_output_book_to_jsonl.py` to analyze characters and generate metadata. You'll be prompted for a protagonist's name to properly attribute first-person references.
   5. Run `python generate_audiobook.py {book_path}` to generate the audiobook. Choose between single-voice or multi-voice narration. Also, choose the output format audiobook/ audio file.
</details>

## Roadmap

Planned future enhancements:

-  ⏳ Add support for running the app through docker.
-  ⏳ Add support for choosing between various languages which are currently supported by Kokoro.
-  ⏳ Add support for [Zonos](https://github.com/Zyphra/Zonos), Models: https://huggingface.co/Zyphra/Zonos-v0.1-hybrid, https://huggingface.co/Zyphra/Zonos-v0.1-transformer. Zonos supports voices with a wide range of emotions so adding that as a feature will greatly enhance the listening experience.
-  ✅ Create UI using Gradio.
-  ✅ Try different voice combinations using `generate_audio_samples.py` and update the `kokoro_voice_map.json` to use better voices. 
-  ✅ Add support for the these output formats: AAC, M4A, MP3, WAV, OPUS, FLAC, PCM, M4B.
-  ✅ Add support for using calibre to extract the text and metadata for better formatting and wider compatibility.
-  ✅ Add artwork and chapters, and convert audiobooks to M4B format for better compatibility.
-  ✅ Give option to the user for selecting the audio generation format.
-  ✅ Add extended pause when chapters end once chapter recognition is in place.
-  ✅ Improve single-voice narration with a different dialogue voice from the narrator's voice.
-  ✅ Read out only the dialogue in a different voice instead of the entire line in that voice.

## Support

For issues or questions, open an issue on the [GitHub repository](https://github.com/namag-ai/audiobook-creator/issues).

## License

This project is licensed under the GNU General Public License v3.0 (GPL-3.0). See the [LICENSE](LICENSE) file for more details.

## Contributing

Contributions are welcome! Please open an issue or pull request to fix a bug or add features.

---

Enjoy creating audiobooks with this project! If you find it helpful, consider giving it a ⭐ on GitHub.
