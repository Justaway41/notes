
# Improving Nepali OCR Accuracy Using LLM-Based Correction

## 🔍 Background

ocr bhaneko ekdum nei vast ra image processing ko scenario ma ekdum nei dherei research gareko topic maddhe parcha  
despite of this being researched and worked on etro barsha dekhi the results are still inconsistent, even for vastly speaking language like english, ocr models haru 100% accurate bhaisakeko chaina  
in the context of nepali language ocr, ekdum nei minimal research/work has been put forward, there are numerous active research happening for improving nepali ocr tei pani accuracy is still on the lower side. 

## 💡 Idea

in this project i have tried to propose a possible fix, or at least a band aid to the wound jasto solution for improving the accuracy for nepali ocr, maile implement gareko model surely extracts texts from pdf and images but kati ko accurate cha bhanne kura na padhe samma we can never know, so this is what i did:  

**i made an LLM read it ani fix it**, for example maile yeuta pdf ma:

```
“mero naam kritartha ho”
```

bhanera lekheko thiye re ani maile teslai scan garna khoje

## ⚠️ Common OCR Problem

mero ocr le for some reason “mero” lai “maro” bhanera extract garyo, in nepali vocabulary, “ma”, “me” and “ro” 3 tei exist garcha  
but inaru bata kun chai 2 ta lai jodda word form huncha bhanne kura ma “maro” does not make sense, 

## 🔧 Solution Using LLM

so what i am doing now is:  
maile extract gareko text is being stored as a **plain text**  
ani maile yo plain text lai **nepali language ko lagi fine tune gareko gemini model** ma pathairachu **chunk garera**,  

ani chunk chai maile esari garirachu ki **each chunk has the context of previous chunk**,  
yo gareko karand le mero **LLM ko contextual awareness badhcha** ani **better output dincha**. 

## 🧾 Final Processing

- the LLM takes the chunked plain texts as input and analyzes the chunks  
- chunks haru ma pathako sentences haru **grammatically sensible cha ki chaina** bhanera hercha  
- if chaina bhane, LLM will return me the **output with corrected grammar**

and since llm lai feed garda texts were chunked, output dida **based on chunks it will append the chunks to form paragraphs**

---

# 🧼 Image Preprocessing Steps in Our OCR Pipeline

This project uses several important image preprocessing techniques to improve the accuracy of OCR (Optical Character Recognition). These steps help clean up the image, highlight the text, and remove noise before the OCR engine tries to read the text.

---

## Why is Preprocessing Important for Nepali Language OCR?

- **Nepali script (Devanagari)** has complex characters, matras (diacritics), and sometimes closely spaced letters.
- Scanned documents in Nepali often have noise, faded ink, or uneven lighting, making it harder for OCR to work directly.
- Preprocessing ensures that these characters are clear and separated, which is crucial for accurate recognition.

---

## Preprocessing Techniques Used

### 1. Grayscale Conversion

- **What:** Converts a color image (RGB or RGBA) into a grayscale image (single channel).
- **Why:** Simplifies the image and reduces the amount of data to process, making it easier to focus on the text.
- **How to Present:**  
  - Tell the teachers: “This step reduces the image to just brightness values, removing color distractions. It’s the foundation for all further processing and is especially important for Nepali documents, which may be scanned in color but only need the text.”

---

### 2. Adaptive Gaussian Thresholding

- **What:** Turns the grayscale image into a black-and-white (binary) image by calculating a local threshold for each small region.
- **Why:** Works well even if the lighting is uneven, making the text stand out more clearly.
- **How to Present:**  
  - Explain: “Instead of using a single threshold for the whole image, this method adapts to different lighting in different parts of the document. This is very useful for old or poorly scanned Nepali documents, where some areas might be lighter or darker.”

---

### 3. Erosion

- **What:** Shrinks the white areas in the binary image.
- **Why:** Removes small white noise and separates connected text or shapes.
- **How to Present:**  
  - Say: “Erosion helps clean up small specks or noise that could confuse the OCR. For Nepali, where characters can be close together, this step helps prevent them from merging.”

---

### 4. Dilation

- **What:** Expands the white areas in the image.
- **Why:** Fills in small gaps and fixes broken parts of the text.
- **How to Present:**  
  - Mention: “After erosion, some parts of the text might become too thin or broken. Dilation restores the thickness and continuity of the characters, which is important for the detailed shapes in Nepali script.”

---

## Where is this in the code?

All these steps are combined in the `preprocess_image` function:

```python
# ocr_api/ocr_utils.py#L38-54
def preprocess_image(image):
    img_array = np.array(image)
    if len(img_array.shape) == 2:  # Grayscale
        gray = img_array
    elif len(img_array.shape) == 3 and img_array.shape[2] == 3:  # RGB
        gray = cv2.cvtColor(img_array, cv2.COLOR_RGB2GRAY)
    elif len(img_array.shape) == 3 and img_array.shape[2] == 4:  # RGBA
        gray = cv2.cvtColor(img_array, cv2.COLOR_RGBA2GRAY)
    else:
        raise ValueError("Unsupported image format for OCR preprocessing.")
    gray = cv2.adaptiveThreshold(gray, 255, cv2.ADAPTIVE_THRESH_GAUSSIAN_C,
                                 cv2.THRESH_BINARY, 31, 2)
    kernel = np.ones((1, 1), np.uint8)
    gray = cv2.erode(gray, kernel, iterations=1)
    gray = cv2.dilate(gray, kernel, iterations=1)
    return Image.fromarray(gray)
```

<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAyNjk3MTA4M119
-->