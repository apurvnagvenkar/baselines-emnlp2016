Phrase-based Machine Translation is State-of-the-Art for Automatic Grammatical Error Correction
===============================================================================================

This repository contains baseline models, training scripts, and
instructions on how to reproduce our results for our state-of-art grammar
correction system from M. Junczys-Dowmunt, R. Grundkiewicz: [_Phrase-based
Machine Translation is State-of-the-Art for Automatic Grammatical Error
Correction_](http://www.aclweb.org/anthology/D/D16/D16-1161.pdf), EMNLP 2016.


Citation
--------

    @InProceedings{junczysdowmunt-grundkiewicz:2016:EMNLP2016,
      author    = {Junczys-Dowmunt, Marcin  and  Grundkiewicz, Roman},
      title     = {Phrase-based Machine Translation is State-of-the-Art for
                   Automatic Grammatical Error Correction},
      booktitle = {Proceedings of the 2016 Conference on Empirical Methods in
                   Natural Language Processing},
      month     = {November},
      year      = {2016},
      address   = {Austin, Texas},
      publisher = {Association for Computational Linguistics},
      pages     = {1546--1556},
      url       = {https://aclweb.org/anthology/D16-1161}
    }


Updates
-------

**Last update: 15/7/2017**

### Results on JFLEG data sets

Outputs generated by our models for the JFLEG data sets are available in the
folder `jfleg`. These are produced by our systems tuned on M^2.  See the README
in that folder for more information.

*New*: We also report results for the systems tuned on GLEU using JFLEG dev.

### Update on phrase tables

The phrase table that we have made publicly available for download were
filtered for CoNLL test sets.  The evaluation of our systems with that PT on
other data sets makes no sense.  Now, we provide the original unfiltered phrase
table in binarized format (due to its size).  The outputs for CoNLL test sets
produced with a binarized PT should remain unchanged.

All .ini files and instructions how to use them have been updated.

### Update on CCLM+sparse models

We have updated the model which use CCLM and sparse features. That model was
used to generate results reported in the paper as _Best sparse + CCLM_. Moses
.ini files are available in the folder `models`.

We also provide the script `models/run_gecsmt.py` to run our models (see notes
below).

### Update for 10gec dataset

The results reported in the camera-ready version of the paper on the dataset
from _Bryant and Ng (2015)_ (Tab. 4, three last columns) are understated due to
the invalid preparation of the M2 file. The correct scores are as follows:

| System | Prec. | Recall | M^2 |
| :--- | --- | --- | --- |
| Baseline    | 69.22 | 37.00 | 58.95 |
| +CCLM       | 76.66 | 36.39 | 62.77 |
| Best dense  | 71.11 | 37.44 | 60.27 |
| +CCLM       | 79.76 | 39.52 | 66.27 |
| Best sparse | 76.48 | 35.99 | 62.43 |
| +CCLM       | 80.57 | 39.74 | 66.83 |

We would like to thank Shamil Chollampatt for reporting this issue!


Outputs
-------

Outputs generated by our models for the CoNLL-2014 test set are available in
the folder `outputs`. These correspond to Table 4 of our paper. See the README
in that folder for more information.


Baseline models
---------------

You can download and run [our baseline
models](http://data.statmt.org/romang/gec-emnlp16/models.tgz) (1,3G).


    models/
    ├── data
    │   ├── lm.cor.kenlm
    │   ├── osm.kenlm
    │   ├── phrase-table.0-0.gz
    │   └── phrase-table.0-0.unfiltered.minphr
    ├── moses.dense-cclm.mert.avg.ini
    ├── moses.dense.mert.avg.ini
    ├── moses.sparse-cclm.mert.avg.ini
    ├── moses.sparse.mert.avg.ini
    └── sparse
        ├── moses.cc.sparse
        └── moses.wiki.sparse

The four configuration `*.ini` files corresponds to the last four systems
described in Table 4.

To use the models you need to install [Moses
decoder](https://github.com/moses-smt/mosesdecoder) (branch `master`). It has
to be compiled with support for 9-gram kenLM language models, and binarized
tables by providing path to CMPH library (see details
[here](http://www.statmt.org/moses/?n=Advanced.RuleTables#ntoc3)), e.g.:

    /usr/bin/bjam -j16 --max-kenlm-order=9 --with-cmph=/path/to/cmph

The language model data are available in separate packages:

* [Wikipedia language model](http://data.statmt.org/romang/gec-emnlp16/wikilm.tgz) (22G)
* [Common Crawl language model](http://data.statmt.org/romang/gec-emnlp16/cclm.tgz) (26G)

The packages contain:

    wikilm/
    ├── wiki.blm
    ├── wiki.classes.gz
    └── wiki.wclm.kenlm
    cclm/
    ├── cc.classes.gz
    ├── cc.kenlm
    └── cc.wclm.kenlm

Adjust absolute paths in `moses.*.ini` files. You can do this by replacing
`/path/to/` with the path to the directory where you downloaded models and
language models. Finally, run _moses_, e.g.:

    /path/to/mosesdecoder/bin/moses -f moses.dense.mert.avg.ini < input.txt

The input file should contain one sentence per line and each sentence has to
follow the Moses tokenization and truecasing as it is presented in
`train/run_cross.perl`.

Alternatively you can use the script `models/run_gecsmt.py`, which performs
pre- and postprocessing, e.g.:

    python ./run/run_gecsmt.py -f moses.ini -w workdir -i input.txt -o output.txt

It can be used to evaluate M2 input:

    python ./run/run_gecsmt.py -f moses.ini -w workdir -i test2014.m2 --m2

You will need to provide paths to Moses, Lazy and this repository. Use `--help`
option for more details.

Running our models might give slightly different results (up to +/- 0.0020
F-score) than the results presented in the paper due to the different versions
of the official CoNLL-2014 test set (we used the version provided during the
CoNLL shared task), M2Scorer, NLTK tokenizer, Moses, and the LM used for
truecasing.


Training models
---------------

Training is described in the README in the folder `train`.


Acknowledgments
---------------

This project was partially funded by the Polish National Science Centre (Grant
No. 2014/15/N/ST6/02330).
