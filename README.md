## WMT CommonCrawl Raw Data Deduplicator for Indian Languages

- The [language-wise raw data](http://data.statmt.org/ngrams/raw/) was obtained by Statistical Machine Translation ([statmt.org](https://statmt.org)).
- For a few languages, the [deduped data is available](http://statmt.org/ngrams/deduped/) but not for Indian Languages.
- The size of CommonCrawl processed dumps are available [here](http://statmt.org/ngrams/pages/raw-data.html).

Aim of this repo:
- To analyze the available lang-wise stats for Indian languages in WMT CommonCrawl releases.
- To create a mono-lingual corpora for Indian languages using statmt.org's CC dumps.

Notes:
- You can run in parallel for many Indian languages.
- For Tamil & Hindi, it took around half a day to dedupe, but for Punjabi, it took not even 1 hour.
- Average compute required per language: 4-8GB RAM, 2 i5 cores, 100GB SSD
- To download all data, I used Axel, a CLI download accelerator (`sudo apt install axel`)
- For supported Indian languages, check `utils.py`
- Last tested on November 2019

### Requirements
- Python 3
- `git clone repo`
- `cd repo`
- `pip3 install -r requirements.txt`

### Setting up Indic NLP Library
- `git clone https://github.com/anoopkunchukuttan/indic_nlp_library.git`
- `git clone https://github.com/anoopkunchukuttan/indic_nlp_resources.git`
- export PYTHONPATH=$PYTHONPATH:\`pwd\`/indic_nlp_library/src/
- export INDIC_RESOURCES_PATH=\`pwd\`/indic_nlp_resources

### Downloading and extracting raw data

For instance, to get all Tamil raw data files:
```
python3 download_list_gen.py --lang_code ta
cd downloads/tamil/
./downloader.sh
cd -
```
where `ta` is the language code (ISO 639-1) for Tamil.

### Processing and de-duplicating raw data

To run for Tamil:
```
python3 processor.py --lang_code ta --folder downloads/tamil/
```

After the run, you will find the following files in `logs/tamil/`:
- `parsed_final.txt` - where all the deduped sentences can be found. (can be changed using `--out_file`)
- `stats.log` - stores the processing stats like #true_tokens, #sentences
- `sent_hashes.pkl` - set of all (Murmur) hashes of all deduped sentences
- `tokens.pkl` - set of all unique tokens

### Optional

To get a sample from the extracted text, you may do:
```
cd logs/tamil/
head -n 10000 parsed_final.txt | tail -n 1000 >parsed_final.sample
cd -
```

To compress all data (maybe to store to a persistent storage), you can do:
```
cd logs/
7z a tamil.7z tamil/
cd -
```
(Ensure you have the 7zip tool installed [`sudo apt-get install p7zip-full`])

### References
- [Indic NLP Examples](https://github.com/anoopkunchukuttan/indic_nlp_library/blob/master/contrib/indic_scraper_project_sample.ipynb)
- [Utils](https://github.com/divkakwani/indian-corpora/blob/master/corpora/utils.py) - For Indic NLP functions

### To Do
- Parallelize the code (MapReduce?)
- Also if possible, try to use the CC dedupe [official code](https://github.com/kpu/preprocess/blob/master/preprocess/commoncrawl_dedupe_main.cc) and compare against ours.
- Check if it works for all languages of the Indian sub-continent.
- Read the paper [N-gram Counts and Language Models from the Common Crawl](http://statmt.org/ngrams/BuckEtAl_LREC2014_CommonCrawlLM.pdf)