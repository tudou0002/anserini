# Anserini Regressions: MS MARCO Passage Ranking

**Models**: BM25 with doc2query-T5 expansions

This page documents regression experiments on the [MS MARCO passage ranking task](https://github.com/microsoft/MSMARCO-Passage-Ranking) with BM25 on [docTTTTTquery](https://github.com/castorini/docTTTTTquery) (also called doc2query-T5) expansions, as proposed in the following paper:

> Rodrigo Nogueira and Jimmy Lin. [From doc2query to docTTTTTquery.](https://cs.uwaterloo.ca/~jimmylin/publications/Nogueira_Lin_2019_docTTTTTquery-latest.pdf) December 2019.

These experiments are integrated into Anserini's regression testing framework.

The exact configurations for these regressions are stored in [this YAML file](../../src/main/resources/regression/msmarco-passage-docTTTTTquery.yaml).
Note that this page is automatically generated from [this template](../../src/main/resources/docgen/templates/msmarco-passage-docTTTTTquery.template) as part of Anserini's regression pipeline, so do not modify this page directly; modify the template instead.

From one of our Waterloo servers (e.g., `orca`), the following command will perform the complete regression, end to end:

```
python src/main/python/run_regression.py --index --verify --search --regression msmarco-passage-docTTTTTquery
```

## Indexing

Typical indexing command:

```
target/appassembler/bin/IndexCollection \
  -collection JsonCollection \
  -input /path/to/msmarco-passage-docTTTTTquery \
  -generator DefaultLuceneDocumentGenerator \
  -index indexes/lucene-index.msmarco-passage-docTTTTTquery/ \
  -threads 18 -storePositions -storeDocvectors -storeRaw \
  >& logs/log.msmarco-passage-docTTTTTquery &
```

The directory `/path/to/msmarco-passage-docTTTTTquery` should be a directory containing `jsonl` files containing the expanded passage collection.
[Instructions in the docTTTTTquery repo](http://doc2query.ai/) explain how to perform this data preparation.

For additional details, see explanation of [common indexing options](../../docs/common-indexing-options.md).

## Retrieval

Topics and qrels are stored [here](https://github.com/castorini/anserini-tools/tree/master/topics-and-qrels), which is linked to the Anserini repo as a submodule.
The regression experiments here evaluate on the 6980 dev set questions; see [this page](../../docs/experiments-msmarco-passage.md) for more details.

After indexing has completed, you should be able to perform retrieval as follows:

```
target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-passage-docTTTTTquery/ \
  -topics tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
  -topicReader TsvInt \
  -output runs/run.msmarco-passage-docTTTTTquery.bm25-default.topics.msmarco-passage.dev-subset.txt \
  -bm25 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-passage-docTTTTTquery/ \
  -topics tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
  -topicReader TsvInt \
  -output runs/run.msmarco-passage-docTTTTTquery.bm25-tuned.topics.msmarco-passage.dev-subset.txt \
  -bm25 -bm25.k1 0.82 -bm25.b 0.68 &

target/appassembler/bin/SearchCollection \
  -index indexes/lucene-index.msmarco-passage-docTTTTTquery/ \
  -topics tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
  -topicReader TsvInt \
  -output runs/run.msmarco-passage-docTTTTTquery.bm25-tuned2.topics.msmarco-passage.dev-subset.txt \
  -bm25 -bm25.k1 2.18 -bm25.b 0.86 &
```

Evaluation can be performed using `trec_eval`:

```
tools/eval/trec_eval.9.0.4/trec_eval -c -m map tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-default.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -M 10 -m recip_rank tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-default.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-default.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-default.topics.msmarco-passage.dev-subset.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -M 10 -m recip_rank tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned.topics.msmarco-passage.dev-subset.txt

tools/eval/trec_eval.9.0.4/trec_eval -c -m map tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned2.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -M 10 -m recip_rank tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned2.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.100 tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned2.topics.msmarco-passage.dev-subset.txt
tools/eval/trec_eval.9.0.4/trec_eval -c -m recall.1000 tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.bm25-tuned2.topics.msmarco-passage.dev-subset.txt
```

## Effectiveness

With the above commands, you should be able to reproduce the following results:

| **AP@1000**                                                                                                  | **BM25 (default)**| **BM25 (tuned)**| **BM25 (tuned2)**|
|:-------------------------------------------------------------------------------------------------------------|-----------|-----------|-----------|
| [MS MARCO Passage: Dev](https://github.com/microsoft/MSMARCO-Passage-Ranking)                                | 0.2805    | 0.2850    | 0.2893    |
| **RR@10**                                                                                                    | **BM25 (default)**| **BM25 (tuned)**| **BM25 (tuned2)**|
| [MS MARCO Passage: Dev](https://github.com/microsoft/MSMARCO-Passage-Ranking)                                | 0.2723    | 0.2768    | 0.2816    |
| **R@100**                                                                                                    | **BM25 (default)**| **BM25 (tuned)**| **BM25 (tuned2)**|
| [MS MARCO Passage: Dev](https://github.com/microsoft/MSMARCO-Passage-Ranking)                                | 0.8192    | 0.8190    | 0.8277    |
| **R@1000**                                                                                                   | **BM25 (default)**| **BM25 (tuned)**| **BM25 (tuned2)**|
| [MS MARCO Passage: Dev](https://github.com/microsoft/MSMARCO-Passage-Ranking)                                | 0.9470    | 0.9471    | 0.9506    |

Explanation of settings:

+ The setting "default" refers the default BM25 settings of `k1=0.9`, `b=0.4`.
+ The setting "tuned" refers to `k1=0.82`, `b=0.68`, tuned on _on the original passages_, as described in [this page](../../docs/experiments-msmarco-passage.md).
+ The setting "tuned2" refers to `k1=2.18`, `b=0.86`, tuned to optimize for recall@1000 directly _on the expanded passages_ (in 2020/12); this is the configuration reported in the Lin et al. (SIGIR 2021) Pyserini paper.

## Additional Implementation Details

Note that prior to December 2021, runs generated with `SearchCollection` in the TREC format and then converted into the MS MARCO format give slightly different results from runs generated by `SearchMsmarco` directly in the MS MARCO format, due to tie-breaking effects.
This was fixed with [#1458](https://github.com/castorini/anserini/issues/1458), which also introduced (intra-configuration) multi-threading.
As a result, `SearchMsmarco` has been deprecated and replaced by `SearchCollection`; both have been verified to generate _identical_ output.

The commands below have been retained for historical reasons only, since they correspond to previously published results.

The following command generates with `SearchMsmarco` the run denoted "BM25 (tuned)" above (`k1=0.82`, `b=0.68`), which corresponds to the entry "docTTTTTquery" dated 2019/11/27 on the [MS MARCO Passage Ranking Leaderboard](https://microsoft.github.io/msmarco/):

```bash
$ sh target/appassembler/bin/SearchMsmarco -hits 1000 -threads 8 \
    -index indexes/lucene-index.msmarco-passage-docTTTTTquery \
    -queries tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
    -k1 0.82 -b 0.68 \
    -output runs/run.msmarco-passage-docTTTTTquery.1

$ python tools/scripts/msmarco/msmarco_passage_eval.py \
    tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.1

#####################
MRR @10: 0.27680089370991834
QueriesRanked: 6980
#####################
```

This corresponds to the scores reported in the following paper:

> Rodrigo Nogueira and Jimmy Lin. [From doc2query to docTTTTTquery.](https://cs.uwaterloo.ca/~jimmylin/publications/Nogueira_Lin_2019_docTTTTTquery-latest.pdf) December 2019.

And are identical to the scores reported in [the docTTTTTquery repo](https://github.com/castorini/docTTTTTquery).

The following command generates with `SearchMsmarco` the run denoted "BM25 (tuned2)" above (`k1=2.18`, `b=0.86`).
This does _not_ correspond to an official leaderboard submission.

```bash
$ sh target/appassembler/bin/SearchMsmarco -hits 1000 -threads 8 \
    -index indexes/lucene-index.msmarco-passage-docTTTTTquery \
    -queries tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
    -k1 2.18 -b 0.86 \
    -output runs/run.msmarco-passage-docTTTTTquery.2

$ python tools/scripts/msmarco/msmarco_passage_eval.py \
    tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.2

#####################
MRR @10: 0.281560751807885
QueriesRanked: 6980
#####################
```

This corresponds to the scores reported in the Lin et al. (SIGIR 2021) Pyserini paper.

As of February 2022, following resolution of [#1730](https://github.com/castorini/anserini/issues/1730), BM25 runs for the MS MARCO leaderboard can be generated with the commands below.
For parameters `k1=0.82`, `b=0.68`:

```
$ sh target/appassembler/bin/SearchCollection \
    -index indexes/lucene-index.msmarco-passage-docTTTTTquery/ \
    -topics tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
    -topicreader TsvInt \
    -output runs/run.msmarco-passage-docTTTTTquery.1 \
    -format msmarco \
    -bm25 -bm25.k1 0.82 -bm25.b 0.68

$ python tools/scripts/msmarco/msmarco_passage_eval.py \
    tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.1

#####################
MRR @10: 0.27680089370991834
QueriesRanked: 6980
#####################
```

For parameters `k1=2.18`, `b=0.86`:

```
$ sh target/appassembler/bin/SearchCollection \
    -index indexes/lucene-index.msmarco-passage-docTTTTTquery/ \
    -topics tools/topics-and-qrels/topics.msmarco-passage.dev-subset.txt \
    -topicreader TsvInt \
    -output runs/run.msmarco-passage-docTTTTTquery.2 \
    -format msmarco \
    -bm25 -bm25.k1 2.18 -bm25.b 0.86

$ python tools/scripts/msmarco/msmarco_passage_eval.py \
    tools/topics-and-qrels/qrels.msmarco-passage.dev-subset.txt runs/run.msmarco-passage-docTTTTTquery.2

#####################
MRR @10: 0.281560751807885
QueriesRanked: 6980
#####################
```

Note that the resolution of [#1730](https://github.com/castorini/anserini/issues/1730) did not change the results.
