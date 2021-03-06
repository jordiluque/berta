# BERTa: RoBERTa-based Catalan language model

## Model description

BERTa is a transformer-based masked language model for the Catalan language. 

It is based on the [RoBERTA](https://github.com/pytorch/fairseq/tree/master/examples/roberta) base model 

and has been trained on a medium-size corpus collected from publicly available corpora and crawlers.

The pretrained model is available on the HuggingFace model hub under the name: **https://huggingface.co/bsc/roberta-base-ca-cased** 
## Training corpora and preprocessing

The training corpus consists of several corpora gathered from web crawling and public corpora.

The publicly available corpora are:

 1. the Catalan part of the [DOGC](http://opus.nlpl.eu/DOGC-v2.php) corpus, a set of documents from the Official Gazette of the Catalan Government

    

 2. the [Catalan Open Subtitles](http://opus.nlpl.eu/download.php?f=OpenSubtitles/v2018/mono/OpenSubtitles.raw.ca.gz), a collection of translated movie subtitles

    

 3. the non-shuffled version of the Catalan part of the [OSCAR](https://traces1.inria.fr/oscar/) corpus \\\\cite{suarez2019asynchronous}, 

    a collection of monolingual corpora, filtered from [Common Crawl](https://commoncrawl.org/about/)

    

 4. The [CaWac](http://nlp.ffzg.hr/resources/corpora/cawac/) corpus, a web corpus of Catalan built from the .cat top-level-domain in late 2013

    the non-deduplicated version

 5. the [Catalan Wikipedia articles](https://ftp.acc.umu.se/mirror/wikimedia.org/dumps/cawiki/20200801/) downloaded on 18-08-2020.

The crawled corpora are:

 6. The Catalan General Crawling, obtained by crawling the 500 most popular .cat and .ad domains

 7. the Catalan Government Crawling, obtained by crawling the .gencat domain and subdomains, belonging to the Catalan Government

    

 8. the ACN corpus with 220k news items from March 2015 until October 2020, crawled from the [Catalan News Agency](https://www.acn.cat/)

To obtain a high-quality training corpus, each corpus have preprocessed with a pipeline of operations, including among the others,

sentence splitting, language detection, filtering of bad-formed sentences and deduplication of repetitive contents.

During the process, we keep document boundaries are kept. 

Finally, the corpora are concatenated and further global deduplication among the corpora is applied.

The final training corpus consists of about 1,8B tokens.

## Tokenization and pretraining 

The training corpus has been tokenized using a byte version of [Byte-Pair Encoding (BPE)](https://github.com/openai/gpt-2)

used in the original [RoBERTA](https://github.com/pytorch/fairseq/tree/master/examples/roberta) model with a vocabulary size of 52,000 tokens. 

The BERTa pretraining consists of a masked language model training that follows the approach employed for the RoBERTa base model

with the same hyperparameters as in the original work.

The training lasted a total of 48 hours with 16 NVIDIA V100 GPUs of 16GB DDRAM.

## Downstream tasks

## CLUB: Catalan Language Understanding Benchmark

The CLUB benchmark consists of 5 tasks, that are Part-of-Speech Tagging (POS), Named Entity Recognition (NER), 
Text Classification (TC), Semantic Textual Similarity (STS) and Question Answering (QA).
For more information, refer to the _HuggingFace datasets cards_ and _Zenodo_ links below :
 1. AnCora (POS): 
   
    - Splits info:
         - train: 13,123 examples 
         - dev: 1,709 examples
         - test: 1,846 examples

    - dataset card: https://huggingface.co/datasets/universal_dependencies

    - data source: https://github.com/UniversalDependencies/UD_Catalan-AnCora

 2. AnCora-ner (NER):
    
    - Splits info:
         - train: 10,628 examples 
         - dev: 1,427 examples
         - test: 1,526 examples
       
    - dataset card: https://huggingface.co/datasets/bsc/ancora-ca-ner
    
    - data source: https://zenodo.org/record/4762031#.YKaFjqGxWUk)
   
 3. TeCla (TC):
    
    - Splits info:
         - train: 110,203  examples 
         - dev: 13,786 examples
         - test: 13,786 examples
       
    - dataset card: https://huggingface.co/datasets/bsc/tecla
    
    - data source: **[TeCla](https://doi.org/10.5281/zenodo.4627197)**: consisting of 137k news pieces from the Catalan News Agency ([ACN](https://www.acn.cat/)) corpus

 4. STS-ca (STS):
    
    - Splits info:
         - train: 2,073 examples
         - dev: 500 examples
         - test: 500 examples
       
    - dataset card: https://huggingface.co/datasets/bsc/sts-ca
    
    - data source: https://doi.org/10.5281/zenodo.4529183
   
 5. ViquiQuAD (QA):
    
    - Splits info:
         - train: 11,255 examples 
         - dev: 1,492 examples
         - test: 1,429 examples
       
    - dataset card: https://huggingface.co/datasets/bsc/viquiquad
      
    - data source: https://doi.org/10.5281/zenodo.4562344
   
 6. XQuAD (QA):
   
    - Splits info:
         - test: 1,190 examples
   
   - dataset card: https://huggingface.co/datasets/bsc/xquad-ca
     
   - data source: https://doi.org/10.5281/zenodo.4526223


## Fine-tuning and evaluation
The fine-tuning scripts for the downstream tasks are based on the HuggingFace [**Transformers**](https://github.com/huggingface/transformers) library.
To fine-tune and evaluate on CLUB, run the following commands:

# The Catalan Language Understanding Benchmark (CLUB)

## Fine-tune and evaluate on CLUB
To fine-tune and evaluate BERTa on the CLUB datasets, run the following commands:

```
bash setup_venv.sh
bash finetune_berta_club.sh
```
The commands above will run fine-tuning and evaluation on CLUB and the results will be shown in the _results.json_ file.
and the logs in the _finetune_berta_club.log_ file.

## Results

The official results obtained are:


| Task        | NER (F1)      | POS (F1)   | STS (Pearson)   | TC (accuracy) | QA (ViquiQuAD) (F1/EM)  | QA (XQuAD) (F1/EM) | 
| ------------|:-------------:| -----:|:------|:-------|:------|:----|
| BERTa       | **88.13** | **98.97** | **79.73** | **74.16** | **86.97/72.29** | **68.89/48.87** |
| mBERT       | 86.38 | 98.82 | 76.34 | 70.56 | 86.97/72.22 | 67.15/46.51 |
| XLM-RoBERTa | 87.66 | 98.89 | 75.40 | 71.68 | 85.50/70.47 | 67.10/46.42 |
| WikiBERT-ca | 77.66 | 97.60 | 77.18 | 73.22 | 85.45/70.75 | 65.21/36.60 |



## How to cite
If you use any of these resources in your work, please cite our latest paper:
```bibtex
@misc{armengolestape2021multilingual,
      title={Are Multilingual Models the Best Choice for Moderately Under-resourced Languages? A Comprehensive Assessment for Catalan}, 
      author={Jordi Armengol{-}Estap{\'{e}} and Casimiro Pio Carrino and Carlos Rodriguez-Penagos and Ona de Gibert Bonet and Carme Armentano{-}Oller and Aitor Gonzalez{-}Agirre and Maite Melero and Marta Villegas},
      year={2021},
      eprint={2107.07903},
      archivePrefix={arXiv},
      primaryClass={cs.CL}
}
```
