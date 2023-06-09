# CVD Training 
[University of Washington Spring 2023 CSE 482B Capstone Project - Not actively maintained]

An Android based ECG training application (aimed at the KardiaMobile 6L device) with lesson modules and autogenerated practice questions that all load from a flexible JSON format.

## Setup

Clone the repository and run it in Android Studio. It should look as follows when run in the emulator for the first time:

![image](https://github.com/tngo2001/cvd-training/assets/97496861/97791157-1bee-4467-ad17-56a30de585bb)

## File Overview

- 📁 /PhotoView directory provides Android fragments for PhotoViews with custom zoom controls. It is a dependency from [this repo](https://github.com/Baseflow/PhotoView)
- 📁 /app/src/main/assets have the autogenerated questions
  - image files go in the subdirectory 📁 /images
  - question metadata like answer options goes in  📁 /questions
- 📁 /app/src/main/res have all the standard Android resources like text strings for translations and drawables (the images used for app icons and module graphics)
  - 📁 /app/src/main/res/raw houses all the custom JSON for the modules (not including the question metadata). There are two main files (details in the Module JSON Format section):
    - 🗒️ /app/src/main/res/dictionary.json contains all the dictionary entries
    - 🗒️ /app/src/main/res/modules.json outlines all the modules and references the other "module fragment" json files
- 📁 /app/src/main/java/com/cse482b/cvdtraining is where the main app logic is. Each file has detailed comments and JavaDoc which can be rendered/browsed in Android Studio.

## Module JSON Format

In dictionary.json, each dictionary entry is an object with a word and a definition property and the file itself is just an array of these entries:
```json
{
  "word": "",
  "definition": ""
}
```

In modules.json, we list the module components. This is one big list of the same four objects repeated for every module:
```json
[
  {"name": "Intro"},
  {"fragments": "intro_what_is_an_ecg+intro_what_is_kardiamobile+intro_what_ecgs_look_like+intro_basic_heart+intro_what_are_ecg_parts+intro_conclusion"},
  {"questions": "identifyPeaksSinus"},
  {"activity": "LessonActivity"},
  ...
]
```
The "name" is whats displayed as the title of the module on the homescreen. The "questions" is the name of the .txt file in assets/questions that has the question metadata with the topics relevent to the module. The "activity" property specifies which kind of module we are working with (for now, only "LessonActivity" is supported). Finally, "fragments" is a plus "+" separated string of the names of the .json module fragment files that make up the module.

Each module "fragment" is like a slide in the module. It has any number of images (file name), text and other media in any order and is formatted as a list of objects:
```json
[
  {"image": "picture"},
  {"text": "qwerty"},
  {"text": "uiop"},
  ...
]
```

## Question Generation

See [this repo](https://github.com/SanderGi/ecg-data) (contact metzgera@uw.edu if you have problems accessing it).

The question generation relies on processing of AliveCor ECG
data. Each ECG is a series of samples for each lead of the ECG
device which we clean using a 0.5 Hz high-pass butter-worth filter
(order = 5), followed by power-line filtering for the 50Hz standard
power-line frequency in the US to establish a steady baseline. Peaks
and complexes are identified by finding the zeros of the gradient as
implemented by [NeuroKit](https://neuropsychology.github.io/NeuroKit/cite_us.html).

After each ECG is processed and components/peak timestamps identified, a custom ECG 
rendering algorithm finds an optimal 6second strip in the ECG to render and draws it
on a grid with the same dimensions as the AliveCor ECG visualisations. Then multiple
questions are generated for each image to conserve space (e.g. each image can have a 
asking about heart rate, about arrhythmias, about identifying peaks, etc.). The actual
question information is stored lines in text files grouped by the question topic (for
easy reference in the lesson module json). Each question takes up 3 lines: first the 
question type, then the image filename, and lastly the answer options where the first
option is the correct one and the others are alternatives (the Android app takes care 
of shuffling these options when parsing the metadata):

![image](https://github.com/tngo2001/cvd-training/assets/97496861/8e64db8e-30d7-4086-a301-60167321d630)

## Translations

Full translation support is available both using the default Android resource management for the autogenerated questions and menu text as well as by swapping out the English module JSONs we uploaded with the translated ones. Source files for translations and voice overs can be found [here](https://drive.google.com/drive/folders/1-W5-W7_UX3JqnS4TzvztkY3lJ4VRCoCk).

## Authors

Design: Octavia Stappart

Development: Kevin Choi, Thompson Ngo, Alex Metzger

Lesson Content: Mallika Sansgiri

## Application License

[MIT](https://choosealicense.com/licenses/mit/)

## Data Licence (everything in the assets folder and all the drawables)

All rights reserved.
