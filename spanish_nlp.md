# Common Spanish words.
### NLP on Spanish Language news articles
by Robert Sunderland

In learning Spanish its not uncommon to have a web site give you a list of the 'most common words' or test you on conjugations for the most common verbs.  But it's difficult to identify the sources behind that determination, and those sources may not align with our Spanish education objectives.  It would be helpful if we could determine for ourselves what constitute common words that we can learn to better understand a given corpus of text.

My desire was to better understand recent Spanish language news articles.  So in this case we can obtain from the internet a number of Spanish news posts and determine which words are most commonly used.  We can focus our education around those words and gain a better understanding of what we are reading.  The Python language can facilitate both the automation steps of obtaining the data as well as the Natural Language Processing components, and presentation of the results.  

Since these are publicly available, non-paywalled news articles I felt it was reasonable to pull them from the internet as long as I wasn't being unreasonable.  Unreasonable for most web scraping generally means don't hammer the site with tons of requests.  To this end, I resolved to make the requests synchronously, one at a time, and to store the results in a data structure so I need not ask for the same article more than once.   

My approach to the problem was to obtain a large quantity of  recent news postings in Spanish from the internet.  Starting with a BBC site I was already reading called Mundo, I used the python requests package to pull the html from their main page and explore the raw output for links to specific articles.  I could then loop through all the article links and pull the html for those pages to obtain a corpus of text.  In trying to interpret the html and look for common patterns to delineate article text from spurious information like headers, footers, javascript, etc it became necessary to clean up the html.

The beautifulsoup package allows navigation of the document object model (DOM) for the page to remove non-relevant information and retain the parts of interest.  Most modern pages contain a large amount of scripting and extra tags to allow those scripts to dynamically change the contact.  So the source HTML was awash in script tags and div tags that existed to supply 'class' or 'id' attributes for the CSS styles or scripts.  In the case of the script tags we can loop through the results of `find-all('script')` and decompose those elements to remove them.  

```python
for x in s.find_all('script'):
    x.decompose()
```

If div tags are empty we can remove them outright. An alternative to decompose is the extract function, which removes the tags but returns the contents.
```python
if len(d.get_text(strip=True)) ==0:
    d.extract()
```
We need to be more careful if the div tags contain text because we might need it later.  In this case we can use replace_with_children so that only the tag itself is deleted.
```python
for x in e.find_all('div'):
    x.replace_with_children()
```
Doing this clean up made the html easier to browse through to look for patterns.  The site has topics at the top of the page that are links to articles grouped in to categories.  These generally had the work topic in the url, but two of them had url structures that resembled regular news articles.  These article-like topics tended to include several sections with many pictures and links to other articles; similar to the main page itself.  For initial testing I just ignored these.

For actual article links, the linked text tended to be the title of the article, so I could use that as a key for a local data store.  I also noticed a pattern where the article itself was contained in a 'main' tag, and that the first header tag within it tended to be the title of the article again.  Beyond that I just needed the text from the article itself so calling `get_text()` would suffice.  Since Python encoding defaults to utf-8 there were no concerns with accents or special characters in the code or output.


My first attempt to retrieve the article contents was to call get_text on the entire main tag.  Which yielded problems because words in adjacent tags would be combined, which would make tokenization fail.  So I chose to step through the child tags and append their text to an array.  I could then re-join them with spaces to allow for accurate tokenization.  Unfortunately stepping through in this way yielded duplicate lines of text in the case where tags were nested.  Searching for 'p' tags specifically reduced the problem but there was still duplication.  I experimented with using sets to dedupe the lines, but this did not preserve the order, which might be important.  In the end I settled on putting the lines in a pandas Series and calling drop_duplicates before rejoining them with whitespace.

## NLTK exploration
Now that I had text that could be parsed I assumed I could use a typical NLP flow, but this ended in a roadblock.  You can skip down to the SpaCy section if you're feeling impatient. My first step was to tokenize the text.  NLTK has a number of tokenizing options so I explored a few before I landed on the
RegexpTokenizer because I really only needed the words themselves, the punctuation and whitespace weren't important.  

After that I removed stopwords.   I had some initial concern that I would have to do something special since it was non-english text, but NLTK has support for spanish so things were much the same as they would have been for english.  The difference is that you specify 'spanish' when you pull the stopwords list.  After that a list comprehension was sufficient to remove them from my tokens.
```python
from nltk.corpus import stopwords
stopwrds = stopwords.words('spanish')
tokens2 = [x for x in tokens if x not in stopwrds]
```

It would also be necessary to stem the words so that verb conjugations, gender, and and plurals could all be treated as instances of the same word.  You can also specify 'spanish' to the snowballstemmer so it will change verbs to their infinitive, gendered words to male, and plurals to singular.  Extremely useful but I was seeing some unusual output.  News articles tend to refer to people, places and other proper nouns, and the stemmer was happily chopping them down as well.  Since capitals were removed and the results would be focused on indidual words (not n-grams) this could potentially make them difficult to identify.  So I would either need to remove proper nouns, or leave them untouched so they could spotted by the user.  

Identifying the proper nouns proved to be the beginning of my troubles.  Solutions I could find seemed to center around identifying the parts of speech of all the words from the sentence structure, a process called part-of-speech tagging.  Don't try the sample below as you will get an error.   NLTK pos_tag only supports English and Russian.  Helpfully it tells you this limitation in the error message itself, but this did stop me in my tracks.

```python
from nltk.tag import pos_tag
pos_tag(tokens, lang='esp') #Error text: NotImplementedError: Currently, NLTK pos_tag only supports English and Russian (i.e. lang='eng' or lang='rus')
```


Luckily I have a data science mentor that works in NLP so a quick email later I had the solution.  

## The SpaCy fix
Admittedly, I'm new to SpaCy because NLTK has been able to solve my NLP problems thus far.  But those problems were all in English and it served me well there, and most of the way here in Spanish.  SpaCy had the advantage this time because its POS tagger supported multiple languages, you just had to load the right one up-front.  Most of the languages have an 'efficiency' and an 'accuracy' flavor of the language module.  In my case I went for efficiency with 'es_core_news_sm'.

SpaCy is a little more convenient in that it performs a lot of processing with just one step when you pass your text to the NLP instance created from the module.  Presumably you pay for this with overhead in that it might perform a lot of analysis you don't need.  It could potentially defer processing until a particular attribute is requested, I have not explored the internals.  In my case, it gave me the tokens, their part of speech, and their lemmas.  Lemmatization is similar enough to stemming in this case that it would still work to obtain results.

It's worth noting the SpaCy attributes for each word tend to have a trailing underscore for the human readable version.  So to examining the lemma or POS you need lemma_ or pos_ respectively.  The attributes without underscores are numeric.

With the POS component I was able to exclude proper nouns ('PROPN'), punctuation ('PUNCT') and whitespace ('SPACE').  I could have used the NLTK stopwords I had already pulled but chose to explore the Spacy equivalent, which stores them in your nlp instance under Defaults.stop_words.  I later augmented this list based on some simple words I found to be extremely common.  The lemmas for proper nouns are the words themselves (capitalization intact) so they could potentially be left in the text.  I put the word attributes into a pandas DataFrame with a flag that identified stopwords so I could easily filter for the desired attributes.  

``` python
sw = nlp.Defaults.stop_words
sw = sw.union({'a', 'y'})

posdf = pd.DataFrame([(w.text, w.pos_, w.lemma_, w.lemma_ in sw) for w in doc], columns=['word','POS','lemma', 'stopword'])
posdf2 = posdf[posdf['stopword']==False]
poswords = set(posdf['POS']) - {'PUNCT', 'PROPN','SPACE'}
posdf2 = posdf2[posdf2['POS'].isin(poswords)]
posdf2.sample(10)

``` 
Once the methodology was producing results for a single article I was able to loop through the stored articles to generate a larger dataframe with the words from all the articles.  The dataframe could then be sorted as desired and word frequency output by calling `value_counts()` on the lemma column.  

## Results
Examining the text of 51 unique articles yielded 12397 words, 4568 of which were unique. The most frequent word was 'imagen' (image) that appeared 84 times, país (country) came second with 73 occurrences, and año (year) was third with 71. If you want to tune you vocabulary to recent spanish news the top 50 words you should learn are: 'imagen', 'país', 'año', 'persona', 'mundo', 'recibir', 'querer', 'pasar', 'vida', 'hora', 'llegar', 'isla', 'humano', 'gobierno', 'explicar', 'encontrar', 'problema', 'historia', 'contenido', 'libro', 'talibán', 'médico', 'descarga', 'tipo', 'deber', 'cuerpo', 'internet', 'puerto', 'luz', 'interesar', 'activa él', 'alimento', 'notificación', 'perdertir', 'versión', 'app', 'mujer', 'idea', 'parecer', 'hombre', 'forma', 'siglo', 'dejar', 'mirar', 'contar', 'seguir', 'permitir', 'grupo', 'señalar', 'tratar'.

## other things I could do
* ignore words that generally look like their english counterparts – if we could intuitively discern the meaning of the word it need not be on our vocab list for study.
* Compare text corpus between sites – maybe BBC Mundo tends to use easier words than the local newspaper in panama
* run a classification analysis on the news articles – can I accurately identify articles that will appear in sub-topic sites linked at the top of the page?
* Test the speed of the efficiency SpaCy module vs the accuracy one
* remove quoted phrases – these could be in another language (e.g. song titles, book titles, publications, etc)
