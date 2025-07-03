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

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyODg2NTM3ODldfQ==
-->