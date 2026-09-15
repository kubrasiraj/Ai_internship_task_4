# Project 4 – Image/Text Recognition (OCR)

A simple Python script that reads text out of an image using Tesseract OCR, cleans up the image first so the results are actually accurate, and only keeps text it's confident about.

## What it does

Give it an image, and it will:
1. Convert it to grayscale and clean up noise
2. Apply adaptive thresholding (Otsu's method) to get a sharp black-and-white version
3. Run OCR on it using `pytesseract`
4. Throw away anything the model isn't at least 80% confident about
5. Print the final text and save a copy of the image with boxes drawn around the words it kept, along with their confidence scores

## Why the pre-processing step

Raw images usually have shadows, blur, or uneven lighting, and OCR engines struggle with that. Converting to grayscale strips out the color noise, and thresholding forces every pixel to be either pure black or white, which makes the character edges much easier for Tesseract to read. Skipping this step usually means worse accuracy.

## Why the confidence filter

Tesseract doesn't just return text, it returns text with a confidence score, and not every detection is reliable. Some are educated guesses. This script sets the bar at 80% — if a piece of text scores lower than that, it gets dropped instead of being included in the final output. That way the result is text you can actually trust, not just whatever the engine spat out.

## Requirements

- Python 3
- `pytesseract`
- `opencv-python`
- Tesseract OCR installed separately on your system (pytesseract is just a wrapper, it needs the actual engine)

On Windows, install Tesseract from [UB-Mannheim's build](https://github.com/UB-Mannheim/tesseract/wiki), and if it's not automatically on your PATH, point to it directly in the script:

```python
pytesseract.pytesseract.tesseract_cmd = r"C:\Program Files\Tesseract-OCR\tesseract.exe"
```

## Running it on Google Colab

This was actually developed and tested on Colab, which is the easier route since Tesseract installs with one line and there's no PATH headache like on Windows.

```python
!apt-get install -y tesseract-ocr
!pip install pytesseract opencv-python
```

Then upload your image:

```python
from google.colab import files
uploaded = files.upload()
```

Set `IMAGE_PATH` to the uploaded filename, paste the rest of the script in a cell, and run it. The annotated output image shows up in the Colab file browser on the left, ready to download.

## How to run it (local machine)

1. Put the image you want to scan in the same folder as the script (or use a full path)
2. Update `IMAGE_PATH` at the top of `ocr.py` to point to it
3. Run:
   ```
   python ocr.py
   ```

## Output

- The validated text is printed to the console, along with each word's confidence score and position
- An annotated image (`ocr_output_annotated.png`) is saved showing exactly which words passed the 80% threshold, boxed in green

## Example

Input image with the text "Hello World 2026" produced:

```
Hello World 2026
--------------------------------------------------
  'Hello'  -> confidence: 94%  @ (34,67,115,32)
  'World'  -> confidence: 95%  @ (168,67,134,32)
  '2026'  -> confidence: 96%  @ (322,67,112,32)
```

All three words cleared the 80% bar, so all three made it into the final output.
