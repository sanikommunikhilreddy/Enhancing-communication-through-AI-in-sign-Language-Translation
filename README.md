# Enhancing-communication-through-AI-in-sign-Language-Translation

Venkatanaganikhil reddy Sanikommu - 700746372

Ahalya reddy Choda - 700746558

Prem kumar kanna - 700756204

Kesara chenna praneethreddy - 700756460

PPT link:- https://docs.google.com/presentation/d/1ZfQdMha-X0SlrI0NSUr0CJjrv0yeDLtmdbA3xhQbUtU/edit?usp=sharing


# Text to Sign & Sign to Text: Bridging the Communication Gap

This project aims to develop machine translation systems that facilitate communication between spoken and signed languages. We utilize [SignWriting](https://www.signwriting.org/), a writing system specifically designed for signed languages, as an intermediary representation to enable seamless translation between different modalities.


**Project Components:**

* **SignWriting Utilities:** We utilize the [signwriting] Python library to:
    * Parse and process SignWriting strings.
    * Visualize SignWriting as images for user-friendly interaction.
    * Tokenize SignWriting for use in machine learning models.
* **Machine Translation Models:**
    * **Text to Sign:** We employ sequence-to-sequence models, potentially incorporating attention mechanisms and factored representations, to translate input text into SignWriting.
    * **Sign to Text:** Conversely, we employ models to translate SignWriting representations back into text enabling comprehension.

## Project Setup and Execution

### Environment Setup

1. **Python Environment:** Create a virtual environment using tools like `venv` or `conda` with Python==3.8.11 to manage dependencies and avoid conflicts with other projects. Activate the environment before proceeding.
2. **Dependencies:** Install the required libraries using `uv` and `pip`:

    ```bash
    pip install uv
    ```

    ```bash
    uv pip install -r requirements.txt
    ```

3. **SentencePiece:** Install the SentencePiece library for tokenization:

    ```bash
    sudo apt-get install sentencepiece # For Linux
    brew install sentencepiece # For macOS
    pip install sentencepiece # For windows
    ```

4. **SignWriting Library:** Install the `signwriting` library:

    ```bash
    pip install git+https://github.com/sign-language-processing/signwriting
    ```

5. **Machine Translation Frameworks (Choose One):** `joeynmt` and `Sockeye` require different versions of the package [sacreBLEU](https://github.com/mjpost/sacrebleu), thus results in version conflict if both installed.

    * **Text to Sign (Sockeye):** Install MXNet and Sockeye:

        ```bash
        pip install --pre -f https://dist.mxnet.io/python 'mxnet==2.0.0b20220206'
        pip install sockeye==3.0.13
        ```

    * **Sign to Text (joeynmt):** Install a custom fork of joeynmt that supports source factors:

        ```bash
        pip install git+ssh://git@github.com/J22Melody/joeynmt.git@factors_complete
        ```

6. ***API Server:** Ensure that you have downloaded the [model checkpoint files](https://drive.google.com/drive/folders/12jJdqWYV7j18Yy-O0Od5KxduZ6c3M3Pj?usp=sharing) to [models](./models/) directory before starting the server. Run [Flask](https://flask.palletsprojects.com/) locally for debugging:

    ```bash
    python app.py
    ```
    See [API_spec.md](./API_spec.md) for API specifications.

## Future Goals:

* Expand language support to encompass a broader range of spoken and signed languages.
* Investigate the integration of fingerspelling recognition for proper nouns and other out-of-vocabulary words.
* Explore the use of SignWriting in other NLP tasks, such as sentiment analysis or text summarization for signed languages.

## Model Training

[sign-language-datasets](https://github.com/sign-language-processing/datasets) are used as training data source

### 40K SIGN to TEXT (Bilingual)

Translate from [Formal SignWriting (FSW)](https://tools.ietf.org/id/draft-slevinski-formal-signwriting-09.html) of American Sign Language to American English.

1. Prepare data in `./data_bilingual/` directory:

    ```bash
    python ./scripts/fetch_data_bilingual.py
    ```

    ```bash
    sh ./scripts/preprocess_bilingual.sh`
    ```

2. Train `baseline_transformer_spm_factor_sign+` SOTA model from scratch:

    ```bash
    python -m joeynmt train ./configs/baseline_transformer_spm_factor_sign+.yaml
    ```

3. Test, postprocess and evaluate it:
    ```bash
    sh ./scripts/evaluate.sh baseline_transformer_spm_factor_sign+
    ```

### 100K SIGN to TEXT (Multilingual)

Extend the first from a bilingual setting to a multilingual setting, translate from FSW of 4 signed languages to 4 corresponding spoken languages.

1. Prepare data in `./data/` directory:

    ```bash
    python ./scripts/fetch_data.py
    ```
    ```bash
    sh ./scripts/preprocess.sh
    ```

2. Train `baseline_multilingual` SOTA model from scratch:
    
    ```bash
    python -m joeynmt train ./configs/baseline_multilingual.yaml`
    ```

3. Test, postprocess and evaluate it:

    ```bash
    sh ./scripts/evaluate_multilingual.sh baseline_multilingual
    ```

### 100K TEXT to SIGN (Multilingual)

Translate the reverse direction, from 4 spoken languages to FSW of 4 corresponding signed languages.

1. Prepare data in `./data_reverse/` directory:

    ```bash
    python ./scripts/fetch_data.py
    ```
    ```bash
    sh ./scripts/preprocess_reverse.sh
    ```
    ```bash
    sh ./scripts/sockeye_prepare_factor.sh
    ```

2. Train `sockeye_spoken2symbol_factor_0.1` SOTA model from scratch:
    ```bash
    sh ./scripts/sockeye_train_factor.sh
    ```

3. Test, postprocess and evaluate it:
    ```bash
    sh ./scripts/sockeye_translate_factor.sh sockeye_spoken2symbol_factor_0.1
    ```
    ```bash
    sh ./scripts/sockeye_evaluate_factor.sh sockeye_spoken2symbol_factor_0.1
    ```
    ```bash
    sh ./scripts/sockeye_evaluate_factor_multilingual.sh sockeye_spoken2symbol_factor_0.1
    ```
