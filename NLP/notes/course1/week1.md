# Sentiment Analysis with Logistic Regression

_____________________________________________________

## let's learn about supervised machine learning. specifically logistic regression!

**THE IDEA**:
> we have in hand, "input features" and "outcome labels", we define a prediction function with some parameters data, and then we map our 'input features" to some "output labels", we loop over changes of the parameters of function, untill we get a best predicting function; which is achived when error; the difference between "outcome labes" and "output labels", is minimized!

sentiment analysis falls under this category.

E.g;
we've a tweet, and we want to predict wheather it's positive or negative (in a linguistic way ofc! in a           philosophical way even the negative things might look positve from different perspective!), we use logistic regressional classifier for this task, label "positive" to `1`, and "negative" to `0`.
we process the raw tweets in our "training datasets", extract useful features out of it, then we'll train our logistic regressional classifier while minimizing the error, and then we apply this logistic regresssional classifier to do classification on "deployment datasets"?!

**Steps**
1. extract the features
2. train our classifier
3. then use our classifier!

_______________________________________


## How to extract the features out of datasets?

**THE IDEA**:
> define a vocab by listing all the words appearing in our dataset; e.g. list of words in list of tweets(corpus), these are features extracted, then loop over to this vocab and see which words appear in subject tweet from vocab, assign `1` for appearance and `0` for otherwise, this is our vector represeting the subject tweet. 

**Equation**:

A `(n+1)D` vec rep of mth tweet of a corpus
{% raw %}
  $$ X(m) = \begin{bmatrix} 1 \\ \vdots \\ n^{th}-comp \end{bmatrix} $$
{% endraw %}

logistic regressinal classifier has to learn `n+1` parameters if size of our vocab was `n`. 

since this kind of `(n+1)D` vec has lots of 0s it's called sparse matrix. hence this kind of sparse vector is not a good idea for vocab of bigger sizes.

btw, `+1` in `n+1` corresponds to some extra dimension, bias?, idk! but i imagine it as a unit plane on `(n+1)th` dimension, we point all of our `n` vecs on this plane, idk why! but i think it's to include the posibilty of vector addition as a linear operation may be! idk how it connects to our problem! let's wait, this might resolve in future notes.

_____________________________


## How do we reduce the dimensionality?

let's try to reduce the dimensionality of vecs which represents our tweets to reduce the complexity of the problem.

**THE IDEA**:
> define the so-called frequency table, we take our "training dataset" in which we already know what the labels are, `1` and `0` in our case for positive and negative tweet, build two tables which maps `2-touple` `(word, 1)` and `(word, 0)` to "*number of times that word appears in a tweet*" with label `1` and `0` respectively. then define a `3D` vec for a subject tweet with entries `1`, `positive freq`, and `negative freq`! now a tweet is represented by a `3D` vec instead of a `(n+1)D`!

**Equation**:

A `3D` vec rep of mth tweet of a corpus
{% raw %}
  $$ X(m) = \begin{bmatrix} 1 \\ \sum_{w in m} count(w, 1) \\ \sum_{w in m} count(w, 0) \end{bmatrix} $$
{% endraw %}

but there are few words in tweets which doesn't posses any sentiments, e.g; punctuations, pronouns etc. why are we considering these words as a measure of sentiment of a tweet? we shouldn't right?

_______________


## Preprocessing

let's then preprocess our corpus in such a way that these non-sentiment words don't come into a play of decision of sentiment of a tweet! but ofc we can't eliminate all the non-sentiment words, after all it's a circular problem! so we try to eliminate those well known non-sentimental words in english literature (though this applies to any language!).

punctuations are critical, they help sometimes to help in a specific problem and sometimes it's not needed! though punctuation play a role in a sentiment of a tweet, it's not a big effect after all, so we'll eliminate those!

we'll also eliminate the so-called **STOP WORDS**, this is a list of stop words, e.g; pronouns, conjuctions etc..

to generate the well known english stop words i did the following;

> **INPUT**
> ```
> import nltk
> nltk.download('stopwords')
> from nltk.corpus import stopwords
> print(stopwords.words('english'))
> ```

> **OUTPUT**
> ```
> {"aren't", 'once', 've', 'most', 'do', 'against', 'only', "it's", 'what', 'of', 'some', "didn't", 'any', 'such', 'weren', 'with', 'am', 'them', 'isn', 'until', 'between', 'into', 'then', 'an', 'does', 'don', 'so', 'yours', 'wasn', 'was', "doesn't", "mustn't", 'have', "won't", 'very', "you'll", 'did', 'a', "isn't", 'ours', 'were', 'both', 'too', 'itself', 'ma', 'the', 'on', 'because', "wouldn't", 'didn', 'when', 'haven', 'just', "should've", 'before', 'for', "shan't", 'about', 'its', 'shouldn', 'where', 'same', 'your', 'needn', 'my', 'who', 'they', 'mustn', 'by', 'now', 'themselves', 'hers', 'from', 'theirs', 'in', 'nor', 'yourself', 'if', 'wouldn', 'i', 'd', 'shan', 'below', 'be', 'each', 'her', 'no', 't', 'not', 'y', 'why', 'than', 'been', "haven't", 'all', 'or', 'ourselves', 'she', 'has', 'here', "weren't", 'o', 'after', 'while', 'this', 'll', 'it', 'whom', 'under', 'doesn', 'can', "she's", 'is', "shouldn't", 'over', "couldn't", 'again', 'which', 're', 'he', 'that', "needn't", 'myself', "you've", "you'd", "that'll", 'me', 'himself', 'aren', 'there', 'those', 'had', 'we', 's', 'during', 'other', 'these', 'herself', 'having', 'will', 'm', 'but', 'through', 'should', "don't", 'hasn', 'doing', 'mightn', 'more', 'and', 'you', "hasn't", 'him', 'to', "you're", 'yourselves', 'how', 'hadn', 'off', 'ain', 'above', 'further', "hadn't", "mightn't", 'as', 'at', 'out', 'our', "wasn't", 'their', 'own', 'being', 'up', 'are', 'down', 'couldn', 'few', 'won', 'his'}
> ```

we can also eliminate other symbols such as hashtags or only hashes (`#`), mentions or only ats (`@`), any any other symbols, it depends on the dataset and out goal, what should be and what shouldn't be eliminated! should we eliminate emojis or emoticons?

also, the capitals or lower cased, verbs' tense or any prefixes and sufixes doesn't need to be in our game either! so we STEM them, but how?

it's called **STEMMING**, we stem out the suffixes and prefixes to get the stems, before that we lower all the capitals. [here](https://tartarus.org/martin/PorterStemmer/) is stemming algo.

hence, the words "AI", "Ai" "ai", "aI" are similar in a sentimental way! (should we count capitals as a measure of extra positivity or negativity according to the corresponding tweet? let's make it simpler and don't do it!)

_______________________


## Ungraded Lab: NLP
notebook itself speak a lot, i think i don't need to write any comments on it!
[NLP_C1_W1_lecture_nb_01](https://github.com/XinYaanZyoy/OCBooks/blob/master/NLP/data/NLP_C1_W1_lecture_nb_01.ipynb)

here's steps taken in it;

1. import nltk for NLP
2. import twitter_smaples from nltk corpus for dataset
3. import pyplot from matplotlib for plotting
4. import random for random generation of numbers
5. download twitter_samples from nltk for dataset
6. fetch positive and negative tweets jsons
7. understand the structure of dataset
8. plot a pie chart for these two classes
9. download stopwords from nltk
10. import re for real expression operations
11. import string for string operations
12. import stopwords from nltk corpus to get list
13. import porterStemmer from nltk stem for stemming using porter stemming algo
14. import TweetTokenizer to get list of words
15. remove hyperlinks, RTs, and #s
16. tokenize the string
17. remove the stop words and punctuations
18. stem the list of words using porter stemming
19. import process_tweet from utils, it solves 18 steps in one call. (we need to get the path right to do this!)

__________________________


## Putting all Together
now let's build a matrix out of all these vecs, put all these `3D` vecs representing tweets, let there be m tweets, so the matrix will be of shape $ mx3 $ .

**Equations**:
{% raw %}
  let 
  $$ X_0(m) \buildrel \rm def \over {:=} \sum_{w \in m} count(w, 0) $$ , and 
  $$ X_1(m) \buildrel \rm def \over {:=} \sum_{w \in m} count(w, 1) $$

  $$ X(m) \buildrel \rm def \over {:=} \begin{bmatrix} 1 & X_0(0) & X_1(0) \\ 1 & X_0(1) & X_1(1) \\ 1 & X_0(2) & X_1(2) \\ \vdots & \vdots & \vdots \\ 1 & X_0(m-1) & X_1(m-1) \end{bmatrix} $$
{% endraw %}

in short the process will be;

1. build freq dict (table)
2. initialize null matrix of size $mx3$
3. process all the tweets and extrct its features

________________


## Ungraded Lab: Visualizing word frequencies
it's straight forward,
[NLP_C1_W1_lecture_nb_02](https://github.com/XinYaanZyoy/OCBooks/blob/master/NLP/data/NLP_C1_W1_lecture_nb_02.ipynb), though it's already in the notebook, i've downloaded the module [utils.py](https://github.com/XinYaanZyoy/OCBooks/blob/master/NLP/data/utils.py) from the course.

_______________


## Logistic regression overview
now we have all the training dataset tweets represented by `3D` vecs, but we still need to come up with a pattern in this training dataset, called finding a right parameter for a classifier, after having a right parameter we can successfully classify positive vs negative tweets of our training dataset without even looking at the lables, and it's true even for any arbitrary dataset we take, but would be accurate enough for any dataset of tweets in general, so to deploy our classifier we need to come up with a right parameter of logistic regressional classifier.

but what's the logistic regressional classifier/function?

it's sigmoid function;

{% raw %}
  $$ h(x(i), \theta ) = \frac1{1+\exp(-\theta^T x(i))} $$
{% endraw %}

Why? Why is sigmoid function important? Why does it help here? how does it do classification?

it's not important for now, if we still want some rigorous work on it checkout [KoHam](/KoHam).

sigmoid function looks like this;

{% raw %}
  $$ f(x) = \frac1{1+\exp(-x)} $$
{% endraw %}

![](/OCBooks/NLP/data/sigmoid_plot.png)

we need a threshold to classify things in terms of binary results, and ofc we'll choose the line $ f(x) = 0.5 $ to be that; let 0.5 represent neutral, 0 (though it's debatable!) impossible, and 1 highest possibilty.

key properties of this function are:
- $$ \lim_{n \to \infty} = 1 $$
- $$ \lim_{n \to - \infty} = 0 $$
- $$ f(0) = 0.5 $$

so, at $ x=0 $ the classification is done, if $ x>0 $ f(x) is positive, negative otherwise, except $ x=0 $.

in logistic regression classifier is $ x \equiv \theta^T x(i) $, so the dot product $ \theta^T x(i) $ is what classifies in regressional model, fuzzily i imagine that $ \theta^T $ represents some vector in dual of $ \mathbb{R}^3 $, and all $ x(i) $ are projected on it, if it's perpendicular that's 0! if not perpendicular then either positive or negative with it's length, if we wish to complete this whole picture in a rigorous and geometric way, please checkout [KoHam](/KoHam).

________________


## But How do we find right \(\theta\) ?
$ \theta $s which produces labels that reduces the error; the difference between "outcome labels" and "output labels".
we loop over our training dataset "to train" or "to find the right" model or parameter or classifier. checkout [WIKI_Gradient_descent](https://en.wikipedia.org/wiki/Gradient_descent).

Testing is straight forward, we divide our dataset in to two parts, 80% training and 20% testing, we try to find accuracy by usual fraction: $ \frac{\text{number of tweets got right predicted}}{\text{total number of tweets analyzed}} $, before we apply gradient descent algo. we need to know what the error or [cost function](https://en.wikipedia.org/wiki/Loss_function) is, and i think it's also pretty straight forward idea if we perceive ["linear regression"](https://en.wikipedia.org/wiki/Linear_regression#:~:text=In%20statistics%2C%20linear%20regression%20is,is%20called%20simple%20linear%20regression.).

i know i'm not writing things rigorously, it's because these notes are supposed to be non-rigorous! for more details we know what to do! :)