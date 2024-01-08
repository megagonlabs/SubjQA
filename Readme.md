# SubjQA
SubjQA is a question answering dataset that focuses on subjective (as opposed to factual) questions and answers. The dataset consists of roughly **10,000** questions over reviews from 6 different domains: books, movies, grocery, electronics, TripAdvisor (i.e. hotels), and restaurants. Each question is paired with a review and a span is highlighted as the answer to the question (with some questions having no answer). Moreover, both questions and answer spans are assigned a _subjectivity_ label by annotators. Questions such as _"How much does this product weigh?"_ is a factual question (i.e., low subjectivity), while "Is this easy to use?" is a subjective question (i.e., high subjectivity).

In short, SubjQA provides a setting to study how well extractive QA systems perform on finding answer that are less factual and to what extent modeling subjectivity can imporve thte performance of QA systems.

### 1. 🛠 -- Data collection
The subjQA dataset is constructed based on publicly available review datasets. Specifically, the _movies_, _books_, _electronics_, and _grocery_ categories are constructed using reviews from the [Amazon Review dataset](http://jmcauley.ucsd.edu/data/amazon/links.html). The _TripAdvisor_ category, as the name suggests, is constructed using reviews from TripAdvisor which can be found [here](http://times.cs.uiuc.edu/~wang296/Data/). Finally, the _restaurants_ category is constructed using the [Yelp Dataset](https://www.yelp.com/dataset) which is also publicly available.

The process of constructing SubjQA is discussed in detail in our [paper](https://arxiv.org/abs/2004.14283). In a nutshell, the dataset construction consists of the following steps:
1. First, all _opinions_ expressed in reviews are extracted. In the pipeline, each opinion is modeled as a (_modifier_, _aspect_) pair which is a pair of spans where the former describes the latter. (good, hotel), and (terrible, acting) are a few examples of extracted opinions.
2. Using Matrix Factorization techninques, implication relationships between different expressed opinions are mined. For instance, the system mines that "responsive keys" implies "good keyboard". In our pipeline, we refer to the conclusion of an implication (i.e., "good keyboard" in this examples) as the _query_ opinion, and we refer to the premise (i.e., "responsive keys") as its _neighboring_ opinion. 
3. Annotators are then asked to write a question based on _query_ opinions. For instance given "good keyboard" as the query opinion, they might write "Is this keyboard any good?"
4. Each question written based on a _query_ opinion is then paired with a review that mentions its _neighboring_ opinion. In our example, that would be a review that mentions "responsive keys".
5. The question and review pairs are presented to annotators to select the correct answer span, and rate the subjectivity level of the question as well as the subjectivity level of the highlighted answer span.

### 2. 📊 - Data Format
All files are in standard _csv_ format, and they consist of the following columns:
* ```domain```: The category/domain of the review (e.g., hotels, books, ...).
* ```question```: The question (written based on a query opinion).
* ```review```: The review (that mentions the neighboring opinion).
* ```human_ans_spans```: The span labeled by annotators as the answer.
* ```human_ans_indices```: The (character-level) start and end indices of the answer span highlighted by annotators.
* ```question_subj_level```: The subjectivity level of the question (on a 1 to 5 scale with 1 being the most subjective).
* ```ques_subj_score```: The subjectivity score of the question computed using the [TextBlob](https://textblob.readthedocs.io/en/dev/) package. 
* ```is_ques_subjective```: A boolean subjectivity label derived from ```question_subj_level``` (i.e., scores below 4 are considered as subjective)
* ```answer_subj_level```: The subjectivity level of the answer span (on a 1 to 5 scale with 1 being the most subjective).
* ```ans_subj_score```: The subjectivity score of the answer span computed usign the [TextBlob](https://textblob.readthedocs.io/en/dev/) package.
* ```is_ans_subjective```: A boolean subjectivity label derived from ```answer_subj_level``` (i.e., scores below 4 are considered as subjective)
* ```nn_mod```: The modifier of the neighboring opinion (which appears in the review).
* ```nn_asp```: The aspect of the neighboring opinion (which appears in the review).
* ```query_mod```: The modifier of the query opinion (around which a question is manually written).
* ```query_asp```: The aspect of the query opinion (around which a question is manually written).
* ```item_id```: The id of the item/business discussed in the review.
* ```review_id```: A unique id associated with the review.
* ```q_review_id```: A unique id assigned to the question-review pair.
* ```q_reviews_id```: A unique id assigned to all question-review pairs with a shared question.

_Note: The above columns do not appear in this order in the csv files._

Here is sample row of the dataset:
```
domain,question,review,human_ans_spans,human_ans_indices,question_subj_level,ques_subj_score,is_ques_subjective,answer_subj_level,ans_subj_score,is_ans_subjective,nn_mod,nn_asp,query_mod,query_asp,item_id,review_id,q_review_id,q_reviews_id
electronics,How well does the speaker work for you?,"To those who think these speakers are too quiet, I suggest you check your ""Sound"" settings (in Mac). Go to ""Output"" and check whether ""Logitech"" is selected, or internal speakers. I'm sure Windows instructions are similar -- Control Panel, Sound, something.If that doesn't work, turn your hearing aid up.This thing is incredibly designed, they should win an award for it. I think it was designed around the 13"" Macbook Pro (that's what I have). It nails at least 7 of Dieter Rams' 10 Principles of design, most especially ""Good design is unobtrusive.""My only complaint is that the sound mix is a little mid-heavy for my taste. That's pretty nitpicking for $40 speakers that hang like a ninja behind my laptop monitor, but I thought I'd share that observation as well. ANSWERNOTFOUND",,speakers are too quiet,"(25, 47)",1,0.0,False,1,0.3333333333333333,False,quiet,speaker,bad,speaker,B003VAK1I2,7a6efb45bd32de268c3a7868d313da0a,d38214a266310836090f4e49bc9f6dbb,e9865435e082e8d4f2cad9ecefa685c4
```

### 3. 📈 -- Statistics on the dataset
The question-review pairs from each domain are split into training, development, and test sets. The table below shows the size of the dataset per each domain and split.

| Domain      | Train | Dev | Test | Total |
|-------------|-------|-----|------|-------|
| TripAdvisor | 1165  | 230 | 512  | 1686  |
| Restaurants | 1400  | 267 | 266  | 1683  |
| Movies      | 1369  | 261 | 291  | 1677  |
| Books       | 1314  | 256 | 345  | 1668  |
| Electronics | 1295  | 255 | 358  | 1659  |
| Grocery     | 1124  | 218 | 591  | 1725  |

Based on the subjectivity labels provided by annotators, we observe that 73% of the questions and 74% of the answers in the dataset are subjective. This provides a substantial number of subjective QA pairs as well as a reasonable number of factual questions to compare and constrast the performance of QA systems on each type of QA pairs.

Finally, the next table summarizes the average length of the question, the review, and the highlighted answer span for each category. 

| Domain      | Review Len | Question Len | Answer Len | % answerable |
|-------------|------------|--------------|------------|--------------|
| TripAdvisor | 187.25     | 5.66         | 6.71       | 78.17        |
| Restaurants | 185.40     | 5.44         | 6.67       | 60.72        |
| Movies      | 331.56     | 5.59         | 7.32       | 55.69        |
| Books       | 285.47     | 5.78         | 7.78       | 52.99        |
| Electronics | 249.44     | 5.56         | 6.98       | 58.89        |
| Grocery     | 164.75     | 5.44         | 7.25       | 64.69        |


###  4 - ✍️ Citation
If you are using the dataset, please cite the following in your work:
```
@inproceedings{bjerva20subjqa,
    title = "SubjQA: A Dataset for Subjectivity and Review Comprehension",
    author = "Bjerva, Johannes  and
      Bhutani, Nikita  and
      Golahn, Behzad  and
      Tan, Wang-Chiew  and
      Augenstein, Isabelle",
    booktitle = "Proceedings of the 2020 Conference on Empirical Methods in Natural Language Processing",
    month = November,
    year = "2020",
    publisher = "Association for Computational Linguistics",
}
```

### 5 - Disclosure
The SubjQA dataset is provided "as-is", and its creators make no
represenntation as to its accuracy. Furthermore, the creators
have on obligation to maintain the dataset.

The SubjQA dataset is constructed based on the following datasets and thus contains subsets of their data:
* [Amazon Review Dataset](http://jmcauley.ucsd.edu/data/amazon/links.html) from UCSD
    * Used for _books_, _movies_, _grocery_, and _electronics_ domains
* [The TripAdvisor Dataset](http://times.cs.uiuc.edu/~wang296/Data/) from UIUC's Database and Information Systems Laboratory
    * Used for the _TripAdvisor_ domain
* [The Yelp Dataset](https://www.yelp.com/dataset)
    * Used for the _restaurants_ domain

Consequently, the data within each domain of the SubjQA dataset should be considered under the same license as the dataset it was built upon.
