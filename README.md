
# NLP-Semantics
This project has been created for the exam of Tecnologie del Linguaggio Naturale (NLP) of the Master's Degree course at the University of Turin.

## Setup
- Clone repository
- Create a virtual environment and activate it
- Install all the required libraries through `pip install -r requirements.txt`
- Execute `nltk.download('all')` to download all the datasets from nltk
- Open and launch any notebook

The project is divided in 3 sections:
- Similarity and Word Sense Disambiguation
- FrameNet Mapping
- NASARI Summarization

## Similarity and Word Sense Disambiguation
In this section, we have worked only with [WordNet](https://wordnet.princeton.edu/) to test its functioning and learn its potential.

### Semantic Similarity
The first task to solve was that of Semantic Similarity and this is what we find in the notebook `semantic_distance.ipynb`.

Given two terms as input, the semantic similarity task consists in providing a numerical similarity score that indicates their semantic proximity.
- for example, the similarity between the concepts "car" and "bus" could be 0.8 on a scale [0,1], where 0 means that the senses are completely dissimilar, while 1 means identity

To solve the conceptual similarity task it is possible to exploit the WordNet tree structure.
The input for this task is contained in `data/WordSim353.csv` and it contains 353 pairs of terms used as test sets in various international competitions.
- A numerical value [0,10] is attributed to each pair, which represents the similarity between the elements of the pair

The goal is to implement three WordNet-based similarity measures:
 - **Wu & Palmer** &rarr; $cs(s_1, s_2) = \frac{2\cdot depth(LCS)}{depth(s_1) + depth(s_2)}$
 - **Shortest Path** &rarr; $sim_{path}(s_1, s_2)	 = 2 \cdot depthMax - len(s_1, s_2)$
 - **Leakcock & Chodorow** &rarr; $sim_{LC}(s_1, s_2) = -log \frac{len(s_1, s_2)}{2 \cdot depthMax}$

For each of these similarity measures, compute
- [Spearman correlation index](https://en.wikipedia.org/wiki/Spearman%27s_rank_correlation_coefficient)
- [Pearson's correlation index](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient)

between the results obtained and the target results in the annotated file.

### Word Sense Disambiguation
Word sense disambiguation (WSD) is an open problem of natural language processing, which comprises the process of identifying which sense of a word (i.e. meaning) is used in any given sentence, when the word has a number of distinct senses (polysemy).

Disambiguating word senses has the potential to improve many natural language processing tasks, such as machine translation, question-answering, information retrieval, and text classification.

By far the most well-studied dictionary-based algorithm for sense disambiguation is the [Lesk algorithm](https://en.wikipedia.org/wiki/Lesk_algorithm).
So we implemented the Lesk algorithm and:
 1. We extracted 50 sentences from the [SemCor](http://web.eecs.umich.edu/~mihalcea/downloads.html) corpus (annotated corpus with WN synsets) and disambigued one noun per sentence
 2. We randomized the selection of the 50 sentences and the selection of the term to disambiguate, and returned the average accuracy on 10 runs

Finally, we computed the accuracy of the algorithm.
You find the code of this in the notebook `wsd.ipynb`.

## FrameNet Mapping
Here the task was to map the [FrameNet](https://framenet.icsi.berkeley.edu/fndrupal/) items with the WordNet senses, seen in the previous section.

Therefore, we have selected 5 random frames for each contributor. For each of them, we took:
- Regent of the frame name
- Frame Elements
- Lexical Units

For each of these items we determined (disambiguating) the corresponding synset in WordNet, using a modified version of Lesk's algorithm that also used the hypernyms and hyponyms of each synset as a context.

After that, to evaluate the accuracy of our algorithm, we manually annotated the corresponding meanings in `data/frame_annotations.json`. To do this, we went through each item thoroughly and checked which WordNet synset was the most appropriate.
As you can see in the notebook, we got very good accuracy values.

## NASARI Summarization
[NASARI](http://lcl.uniroma1.it/nasari/) is the vector counterpart of [BabelNet](https://babelnet.org/).
What we did here was to implement an unsupervised algorithm to produce a summary.

We used two relevance criteria to select what to keep and what to delete, based on the topic of the document:
 - **Title**: the title of the document gives information about the content. We can use the words of the title to find the important content within the text. What you do is create a list of words taken from the title (eliminating the stop words) and use the words in the list as keywords to search for important phrases in the text
 - **Cue phrases**: in the texts there are phrases that contain words that allow us to understand that important things (bonus phrases) or useless (stigma phrases) are about to be said. For example, bonus phrases could be "The aim of the present paper is ...", "The purpose of this articole ...", "In this report we outline ...". Such sentences contain comparatives, superlatives, concluding expressions, etc. The stigma phrases contain negations, pronouns, etc. They are phrases that contain stigma such as "hardly", "impossible", etc.
These phrases can be found automatically by assigning a score to a phrase. The score of a sentence increases if it contains bonus phrases; decreases if it contains stigma phrase

The topic can be referred to as a (set of) NASARI vector(s):
- $ vt1 = \lbrack{term_1{\textunderscore}score, term_2{\textunderscore}score, …, term_{10}{\textunderscore}score}\rbrack $
- $ vt2 = \lbrack{term_1{\textunderscore}score, term_2{\textunderscore}score, …, term_{10}{\textunderscore}score}\rbrack $

We determined the context, by collecting the vectors of terms herein. Then, retain paragraphs whose sentences contain the most salient terms, based on
the Weighted Overlap:


\begin{align}  
WO(v_1, v_2) = \frac{\sum_{q \in O}(rank(q, v_1) + rank(q, v_2))^{-1} } {\sum_{i=1}^{|O|} ((2i)^{-1})} 
\end{align}

Finally, we evaluated our summary through two metrics:
 - [BLEU](https://en.wikipedia.org/wiki/BLEU) (BiLingual Evaluation Understudy)
 - [ROUGE](https://en.wikipedia.org/wiki/ROUGE_(metric)) (Recall-Oriented Understudy for Gisting Evaluation)

## Contributing
Libraries used:
- [nltk](https://www.nltk.org/)
- [scipy](https://scipy.org/)

## Authors
 - [Lorenzo Favaro](https://github.com/lorenzofavaro)
 - [Andrea Senese](https://github.com/AndreaSenese)
