# SubjQA
SubjQA is a question answering dataset that focuses on questions (and answer spans) that are mostly subjective (as opposed to factual questions and answers). The dataset consists of roughly 9,000 questions over reviews from 6 different domains: books, movies, grocery, electronics, TripAdvisor (i.e. hotels), and restaurants. The data also contains a subjectivity label for each question as well as the answer span in the dataset.

### 1. 📈 -- Statistics on the dataset
The question-review pairs from each domain are split into training, development, and test sets. The table below summarizes the size of the data in each category.

| Domain      | Train | Dev | Test | Total |
|-------------|-------|-----|------|-------|
| TripAdvisor | 1165  | 230 | 512  | 1686  |
| Restaurants | 1400  | 267 | 266  | 1683  |
| Movies      | 1369  | 261 | 291  | 1677  |
| Books       | 1314  | 256 | 345  | 1668  |
| Electronics | 1295  | 255 | 358  | 1659  |
| Grocery     | 1124  | 218 | 591  | 1725  |

The most important aspect of SubjQA is the subjectivity of the collected questions and answers. 73% of the questions and 74% of the answers in the dataset are labeled as subjective by annotators.

The next table highlights the average length of the question, the review, and the highlighted answer span for each category. 

| Domain      | Review Len | Question Len | Answer Len | % answerable |
|-------------|------------|--------------|------------|--------------|
| TripAdvisor | 187.25     | 5.66         | 6.71       | 78.17        |
| Restaurants | 185.40     | 5.44         | 6.67       | 60.72        |
| Movies      | 331.56     | 5.59         | 7.32       | 55.69        |
| Books       | 285.47     | 5.78         | 7.78       | 52.99        |
| Electronics | 249.44     | 5.56         | 6.98       | 58.89        |
| Grocery     | 164.75     | 5.44         | 7.25       | 64.69        |

### 2. 🛠 -- Dataset collection
The subjQA dataset is constructed based on publicly available review datasets. Specifically, the _movies_, _books_, _electronics_, and _grocery_ categories are constructed using reviews fromo the [Amazon Review dataset](http://jmcauley.ucsd.edu/data/amazon/links.html). The _TripAdvisor_ category, as the name suggests, is constructed using reviews from TripAdvisor which can be found [here](http://times.cs.uiuc.edu/~wang296/Data/). Finally, the _restaurants_ category is constructed using the [Yelp Dataset](https://www.yelp.com/dataset) which is publicly available.

The details of our dataset construction process based on reviews are discussed in detail in our [paper](https://arxiv.org/abs/2004.14283). In a nutshell, the dataset construction follows these steps:
1. First all opinions expressed in reviews are extracted. Each opinion is modeled as (_modifier_, _aspect_) pair. For instance, (good, hotel), (terrible, acting), and etc.
2. Then associations between expressed opinions are mined (through Matrix Factorization). For instance, the system mines that "responsive keys" implies "good keyboard". In the dataset, we refer to the second opinion as the _query_ opinion, and the first opinion as its _neighboring_ opinion. 
3. Annotators are asked to write a question based on the _query_ opinions. For instance, they might write "Is this keyboard any good?"
4. Each question about a _query_ opinion is then paired with a review that expresses the _neighboring_ opinion.
5. The question and review pairs are presented to annotators to select the correct answer span, rate the subjectivity level of the question, and the subjectivity level of the answer that they have highlighted.

### 3. 📊 - Data Format
All files are in standard _csv_ format, and they consist of the following columns:
* ```domain```: The category/domain of the review (e.g., hotels, books, ...)
* ```question```: The text of the question (written about the query opinion)
* ```review```: The text of the review that mentions the opinion related to the query opinion.
* ```human_ans_spans```: The span labeled by annotators as the answer.
* ```human_ans_indices```: The (character-level) start and end indices of the answer span highlighted by the annotators.
* ```question_subj_level```: The subjectiviy level of the question (on a 1 to 5 scale with 5 being the most subjective).
* ```ques_subj_score```: ???
* ```is_ques_subjective```: A boolean subjectivity label derived from ```question_subj_level``` (i.e., scores above ??? are considered as subjective)
* ```answer_subj_level```: The subjectiviy level of the answer span (on a 1 to 5 scale with 5 being the most subjective).
* ```ans_subj_score```: ???
* ```is_ans_subjective```: A boolean subjectivity label derived from ```answer_subj_level``` (i.e., scores above ??? are considered as subjective)
* ```nn_mod```: The modifier of the neighboring opinion (which appears in the review).
* ```nn_asp```: The aspect of the neighboring opinion (which appears in the review).
* ```query_mod```: The modifier of the query opinion (around which a question is manually written).
* ```query_asp```: The aspect of the query opinion (around which a question is manually written).
* ```item_id```: The id of the item/business discussed in the review.
* ```review_id```: A unique id associated with the review.
* ```q_review_id```: A unique id assigned to the question-review pair
* ```q_reviews_id```: ???

_Note: The above columns do not appear in this order in the csv files._

Here is sample row of the dataset:
```
domain,question,review,human_ans_spans,human_ans_indices,question_subj_level,ques_subj_score,is_ques_subjective,answer_subj_level,ans_subj_score,is_ans_subjective,nn_mod,nn_asp,query_mod,query_asp,item_id,review_id,q_review_id,q_reviews_id
electronics,How well does the speaker work for you?,"To those who think these speakers are too quiet, I suggest you check your ""Sound"" settings (in Mac). Go to ""Output"" and check whether ""Logitech"" is selected, or internal speakers. I'm sure Windows instructions are similar -- Control Panel, Sound, something.If that doesn't work, turn your hearing aid up.This thing is incredibly designed, they should win an award for it. I think it was designed around the 13"" Macbook Pro (that's what I have). It nails at least 7 of Dieter Rams' 10 Principles of design, most especially ""Good design is unobtrusive.""My only complaint is that the sound mix is a little mid-heavy for my taste. That's pretty nitpicking for $40 speakers that hang like a ninja behind my laptop monitor, but I thought I'd share that observation as well. ANSWERNOTFOUND",,speakers are too quiet,"(25, 47)",1,0.0,False,1,0.3333333333333333,False,quiet,speaker,bad,speaker,B003VAK1I2,7a6efb45bd32de268c3a7868d313da0a,d38214a266310836090f4e49bc9f6dbb,e9865435e082e8d4f2cad9ecefa685c4
```

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
### 5 - Using the dataset
