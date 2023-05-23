# BCMS-variant-classifier
A classification tool for discriminating between Bosnian, Croatian, Montenegrin, and Serbian.

The tool consists of two files, a vectorizer and a classifier. The development of the system was described in [this paper](https://aclanthology.org/2023.vardial-1.11/), which can be cited as follows:

```latex
@inproceedings{rupnik2023benchic,
  title={BENCHi{\'c}-lang: A Benchmark for Discriminating between Bosnian, Croatian, Montenegrin and Serbian},
  author={Rupnik, Peter and Kuzman, Taja and Ljube{\v{s}}i{\'c}, Nikola},
  booktitle={Tenth Workshop on NLP for Similar Languages, Varieties and Dialects (VarDial 2023)},
  pages={113--120},
  year={2023}
}
```

## Use demo

```python
from transliterate import translit
import pickle
with open("vec.pickle", "rb") as f:
    vec = pickle.load(f)
with open("clf.pickle", "rb") as f:
    clf = pickle.load(f)
 
 
def classify(s: str) -> str:
    s = translit(s, language_code="sr", reversed=True)
    vectors = vec.transform([s]).toarray()
    predictions = clf.predict(vectors).tolist()
    return predictions[0]

print(classify("U njegovoj pratnji su ministarka turizma Fani Pali-Petralija, zamenik ministra ekonomije Petros Dukas i zamenik ministra inostranih poslova Teodoros Kasimis."))
# -> 'sr'
```

Code breakdown:
1. Import the transliteration function and pickle, load the classifier and the vectorizer:
```python
from transliterate import translit
import pickle
with open("vec.pickle", "rb") as f:
    vec = pickle.load(f)
with open("clf.pickle", "rb") as f:
    clf = pickle.load(f)
# This part will raise warnings if there is a mismatch between 
# sklearn version of the pickled elements and the installed version
```
2. Define a function that takes a string as an input, transliterates it to Latin script, vectorizes it, classifies the vector, and returns the prediction:
```python
def classify(s: str) -> str:
    # Transliteration has no effect on Latin characters, 
    # but it transforms Serbian and Montenegrin Cyrillic characters to Latin.
    # Language code can be 'sr' for both Serbian and Montenegrin
    s = translit(s, language_code="sr", reversed=True) 
    # wrap the string in a list and vectorize it:
    vectors = vec.transform([s]).toarray()
    # classify the vectors:
    predictions = clf.predict(vectors).tolist()
    # predictions look like ["sr"], so only return the first element:
    return predictions[0]

```
3. Run the function on a string to be classified.
```python
print(classify("U njegovoj pratnji su ministarka turizma Fani Pali-Petralija, zamenik ministra ekonomije Petros Dukas i zamenik ministra inostranih poslova Teodoros Kasimis."))
```
