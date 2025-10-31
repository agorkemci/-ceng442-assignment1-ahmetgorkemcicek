# -ceng442-assignment1-ahmetgorkemcicek 
NOTE: README is also provided as docx format

FOR EMBEDDINGS: https://drive.google.com/drive/folders/1SV90ktjhmiaX81-t7p0w_NY5c8qiAH1D?usp=sharing

DATA & GOAL
This assignment aims to use 5 datasets in Azerbaijan, which contain labels that are either binary or ternary (negative, neutral, positive). Before moving ahead, we need to clarify the idea of using neutral labels for some sentences to ensure that the sentence cannot be clearly defined as positive or negative, meaning it has no particular sentiment.
For sentiment analysis, we perform some preprocessing, normalization, and other processes like domain tagging. 

PREPROCESSING
Preprocessing Firstly, we examine the sentences to check if they are empty or contain only spaces with no characters. We also need to check for duplicated sentences to achieve better learning models. If there are multiple spaces, they are converted into a single space. Also, if the parameter keep_sentence_punctuation is false, punctuations  at the end of sentences are removed. We normalize punctuations if they repeated more than one time. After that, we continue to normalization. In the first stage of normalization, we convert the characters into lowercase. We also replace English chars into Azerbaijani characters as needed (I->ı and İ->i).  We handle URLs, emojis, and hashtag normalizations. We replace digits with <NUM>. Then, we deasciify the English characters to get the correct representation of words in the Azerbaijani language(cox-> çox). We mostly try to find patterns to replace them. We remove HTML tags that start with < and end with >. We remove https:// and http:// from words and tokenize them as URL. We remove words that start with @ and any digits, characters, or _ are tokenized as USER. We also check the words that contain a character repetead and more than three times, we reduced them  to a single of that character(yoooox -> yoox). Others are documented in minichallenges of readme.

Some Examples:
1. Lowercase & Azerbaijani-aware
Before: "İnkişaf Işıqlı!"  -> After: "inkişaf ışıqı"
2. URL/Email/Phone Replacement
Before: "Contact me at test@mail.com or visit https://example.com" -> After: "contact me at EMAIL or visit URL"
3. Repeat Character Collapse
Before: "coooool movie!" -> After: "coool movie"
4. Number Replacement
Before: "Mən 3 kitab aldım" -> After: "mən <NUM> kitab aldım"




MINI CHALLENGES
The code is already implemented the hashtag split mechanism and Emojis dictionary and mapping related emojis as EMO_POS or EMO_NEG. We create an emoji tag dictionary in order to tokenize them and also for capturing the meaning of emojis. In hashtag split mechanis, we normalize the hashtags to extract their meaning. We collect words that start with #, and for any word, we add a space when converting from lowercase to uppercase to normalize hashtags. 
Examples in below:
Mən bu filmi çox bəyəndim :D -> Mən bu filmi çox bəyəndim EMO_POS"
#QarabagIsBack"   ->  qarabag is back
During sentence splitting and tokenization, if one of the tokens matches our predefined negators such as yox, heç, or qətiyyən  the next three words that follows the negators  will have ” _NEG” appended. The first encountered negator reverses the meaning of the following words, turning negative into positive and vice versa.  
Example:    ['Mən', 'bu', 'filmi', 'heç', 'bəyənmədim_NEG', 'və_NEG', 'yaxşı_NEG', 'olmadı']
Also, in the assignment we had to propose new 10 words to the stopwords removing to get the context for getting better learned models. I added the list of 10 stopwords as: ["hansı","əgər","bəlkə","yaxud","çünki","qədər","necə","bəzi","başqa","sadəcə”]
And we also must make “True” the parameter of “remove_stop_words” of “process_files” function to remove the stopwords to get better context between tokens in the stage of learning of  the  models. I actually tested models for both list the standart list of stopwords give more score in both model (0.001 differences).

Compare results of negation words for nearest neighbors qualitatively:Word2Vec Nearest Neighbors 
NN for 'yaxşı': ['iyi', 'yaxshi', '<RATING_POS>', 'awsome', 'işləməyib']
NN for 'yaxşı_NEG': ['əvvəl_NEG', 'etmək_NEG', 'buna_NEG', 'olmadı_NEG', 'etmirəm_NEG']
NN for 'pis': ['vərdişlərə', 'sürükliyir', 'yaxşıdır_NEG', 'əlinizə_NEG', 'örnək']
NN for 'pis_NEG': ['olmaz_NEG', 'dəyər_NEG', 'kəsə_NEG', 'yoxdu_NEG', 'endirim_NEG']


FastText Nearest Neighbors
NN for 'yaxşı': ['yaxşıı', 'yaxşıkı', 'yaxşıca', 'yaxş', 'yaxşıki']
NN for 'yaxşı_NEG': ['yaxşısı_NEG', 'yaxşılığı_NEG', 'yaxşıdır_NEG', 'yaxşıları_NEG', 'yaxsdir_NEG']
NN for 'pis': ['piis', 'pisdii', 'pisi', 'pi', 'pisdi']
NN for 'pis_NEG': ['əks_NEG', 's_NEG', 'sms_NEG', 'items_NEG', 'tv_NEG']


Additionally, the assigment emhasizes, we have to check the number of tokenized words that deasciified, the main purpose of doing that is trying to improve the lexical consistency. I have created a global variable to count the total number of the deasciified words by slang_mapping. We get the old token word before we checking is the old token  in slang dict. we check the is the new token that replaced and contains the Azeri form of that word. If it is changed that means we deasciified the token, then we increase by one the counter of slanged word. In my model it has value of 4495.

DOMAIN-AWARE
In Domain-aware preprocessing, we are trying to classify  the sentences  for four domain which are news, social, reviews, general.  For domain of news apa, trend, azertac, Reuters or  bloomberg words are included in sentences, the sentences are marked as the domnews. For detecting the sentences that is in domain of socials we investigates rt , @,  # or the emotes in sentences. For reveiew we look at the words of azn, manat, qiymət, aldım, ulduz, çox yaxşı, çox pis in sentences. If there is no detecting of these three domain we marked it as general domain. At the end of the specifying the domains, we add the tags before the column that holds sentences. We create a new column for hold tags for each sentences. After this stages, it looks for special normalization steps for only reviews domain. We look at the az or manat words  to tokenize it as <PRICE>, and “3 ulduz” we normalize them as <STARS_3>. We also look at some signs of any review for domain tagged sentences. For instance, specific expressions like “çox yaxşı”, “ çox pis” in order to tokenize them as  POS_RATE or POS_NEG.

EMBEDDINGS
 	The parameters of Word2Vec and FastText in the chart:
| **Parameter**            | **Word2Vec** | **FastText** |
| ------------------------ | ------------ | ------------ |
| **Vector size**          | 300          | 300          |
| **Window**               | 5            | 5            |
| **Min count**            | 3            | 3            |
| **Epochs**               | 10           | 10           |
| **Skip-gram**            | 1            | 1            |
| **Negative sampling**    | 10           | 10           |
| **Subword n-gram range** | –            | 3–6          |
| **Seed**                 | 42           | 42           |
| **Workers**              | 1            | 1            |



Results:
 
```
Saved: labeled-sentiment_2col.xlsx (rows=2955)
Saved: test_1_2col.xlsx (rows=4198)
Saved: train_3_2col.xlsx (rows=19557)
Saved: train-00000-of-00001_2col.xlsx (rows=41756)
Saved: merged_dataset_CSV_1_2col.xlsx (rows=55662)
Wrote corpus_all.txt with 124353 lines
Saved embeddings.
== lexical coverage (per dataset) ==
labeled-sentiment_2col.xlsx: W2V=0.930, FT(vocab)=0.930
test_1_2col.xlsx: W2V=0.906, FT(vocab)=0.906
train_3_2col.xlsx: W2V=0.909, FT(vocab)=0.909
train-00000-of-00001_2col.xlsx: W2V=0.935, FT(vocab)=0.935
merged_dataset_CSV_1_2col.xlsx: W2V=0.941, FT(vocab)=0.941

== Similarity (higher better for synonyms; lower better for antonyms) ==
Synonyms: W2V=0.335, FT=0.432
Antonyms: W2V=0.319, FT=0.398
Separation (Syn - Ant): W2V=0.016, FT=0.033

== Nearest neighbors (qualitative) ==
W2V NN for 'yaxşı': ['iyi', 'yakshi', '<RATING_POS>', 'awsome', 'islemmeyib']
FT NN for 'yaxşı': ['yaxşı', 'yaxşıkı', 'yaxşıca', 'yaxş', 'yaxşiki']
W2V NN for 'pis': ['verdiyim', 'sürükliyir', 'yakşidir_NEG', 'bilinize_NEG', 'örnek']
FT NN for 'pis': ['piis', 'pisdi', 'pisi', 'pi', 'pisdi']
W2V NN for 'cox': []
FT NN for 'cox': ['coxcox', 'coxh', 'coxm', 'coxmu', 'co']
W2V NN for 'bahalı': ['portretlerine', 'villalari', 'yatakları', 'qabardır', 'metallarla']
FT NN for 'bahalı': ['bahallı', 'bahalısı', 'bahalıq', 'baharlı', 'bahalığı']
W2V NN for 'ucuz': ['şeytanbazardan', 'sinibsa', 'sududu', 'düzəltdirilib', 'satsaq']
FT NN for 'ucuz': ['ucuza', 'ucuzu', 'ucuzdu', 'ucuzluğa', 'ucuzdur']
W2V NN for 'mükəmməl': ['kalınayla', 'möhtəşəmm', 'möhdeşem', 'bayıldım', 'yaradılanların']
FT NN for 'mükəmməl': ['mükemməl', 'mükemel', 'mükəmmel', 'mükəmməldi', 'mükəmməlsiniz']
W2V NN for 'dəhşət': ['xalacandan', 'treylerde', 'içirdim', 'tesirlidi', 'dövrcün']
FT NN for 'dəhşət': ['dehşetdi', 'dəhşətə', 'dəhşəti', 'dəhşətizm', 'dəhşətdi']
W2V NN for '<PRICE>': []
FT NN for '<PRICE>': ['fətihə', 'light', 'flight', 'hurwitz', 'ermes']
W2V NN for '<RATING_POS>': ['süper', 'uygulama', 'deneyin', 'yaradı', 'yaxş']
FT NN for '<RATING_POS>': ['<RATING_NEG>', 'süperr', 'duolingoyu', 'süper', 'coxx']
The no. of deasciified words: 4495
```

REPRODUCIBILITY
Environment: Platform: Google Colab (hosted environment),Python version: 3.12.12, Operating system: Linux (Colab environment), Runtime: CPU, Processor: Intel(R) Xeon(R) CPU @ 2.20GHz, Memory (RAM): 13 GB
Main Library Versions
Library	Version	Description
pandas	2.2.2	Data analysis and manipulation
gensim	4.4.0	Word embeddings and vector space modeling
openpyxl	3.1.5	Excel file reader/writer
regex	2024.11.6	Extended regular expressions module
ftfy	6.3.1	Unicode and text normalization
scikit-learn	1.6.1	Machine learning utilities and evaluation

How to run:
In collab you just have to run the command “!pip install pandas gensim openpyxl regex ftfy scikit-learn” to import required libraries. The parameters of word2vec and fasttext are already setted, you just need to run the main code.


CONCLUSSIONS: 
In the review of results we can observe the fasttext model is better than the word2vec in all ways. In my researches, I saw that the fasttext is working better with morphologically wealthy languages, and the language that we have used has too much suffixes, prefixes etc.Therefore we observed that fasttext worked better than word2wec. However the learning stage is a bit longer than word2wec. I also need to emphasize the negative sampling factor. The negative sampling is an optimization technique that provides number of n samples which has negative correlation between the centered word that we are tried find out the context of it. By providing n samples, we say just select random n samples that has negative correlation with centered word and compute the probabilities. Now, we have get model response because the softmax function does not have to calculate the probabilities of the correlations for each word in dataset.

AHMET GÖRKEM ÇİÇEK – 21050911019
DEPT: SOFTWARE ENGINEERING
