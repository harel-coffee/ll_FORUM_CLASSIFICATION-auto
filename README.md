# CoFET
Download with `git clone` or equivalent.
```bash
git clone @repo
```
* Python 3.x  
* Tensorflow > 1.5
* Sklearn > 0.19.0

## Purpose of CoFET 
An utility python library toolkit to perform education Forum post classification. Main functionalities: text pre-processing, model implementation and training, result evaluation and analysis. The toolkit is divided into four components: Data adapter, Feature Composer, Model Selector and Performance Evaluator


## Data adapter: 
Data adapter is designed to transform raw input data into a proper format to be used in subsequent steps. The input data is expected to be in a csv file which should include a post text column and a label column. As an example, we have included Stanford forum post dataset used in this study to the toolkit repository. The data adaptor component is responsible for pre-processing the raw text contained in a post (e.g., stemming and removing non-alphabetic words). Then, the pre-processed posts are randomly split into training and testing set according to a pre-defined ratio (i.e., 80% for training and 20% for testing).

e.g., 
```python
cofeter.adapt()
```

<!-- Supported functionalities: 
* tokenize: tokenize.word_tokenize()
* stemming (e.g., stemming and removing non-alphabetic words): defaultdict(lambda : wn.NOUN)
* lemmatize: WordNetLemmatizer().lemmatize()
* Training/test set split: CofetEntry.preTrain() -->

|               Function                |                                   Example                                                 |
| :-----------------------------------: | :---------------------------------------------------------------------------------------: |
|            tokenize                   |                ```python tokenize.word_tokenize()    ```                                |
|            stemming                   |                     ```python          defaultdict(lambda : wn.NOUN)    ```            |
|     removing non-alphabetic words     |                    ```python           defaultdict(lambda : wn.NOUN)   ```                |
|           lemmatize                   |                   ```python       WordNetLemmatizer().lemmatize()      ```              |
|      Training/test set split          |                    ```python           CofetEntry.preTrain()           ```        |



## Feature Composer:  
Feature Composer generates a vector representation to represent a post. This vector representation can be used as the input for subsequent classification models. Depending on the selected classification model (traditional ML models vs. DL models), this component either generates a vector consisting of a list of commonly-used features (for traditional ML models), or a embedding-based vector (for DL models). Most of the textual features used in this study (in Section II) are included in the feature composer except for Coh- metrix, LSA similarity and LIWC features as these three features requires external software to generate. However, once generated the features can be easily integrated by simply append the additional feature set to the output feature vector produced by feature composer. As an example,LIWC software9)havetheoptiontoproduce a csv file containing feature set per post, a user may generate LIWC feature using their software and add those features to the output file of this step. For DL models, The embedding vector will be generated using bert-as-a-service, the output will be in 768 dimensional BERT embedding. To enable an efficient evaluation, the generated vectors are stored locally and can be used as input for different models.


|               Function                |                                   Example                                                 |
| :-----------------------------------: | :---------------------------------------------------------------------------------------: |
|            TFIDF                   |                ```python models.util.feature_tfidf    ```                                |
|            NGRAM                   |                     ```python         models.util.feature_ngram    ```            |
|     READABILITY     |                    ```python           models.util.feature_readability ```                |
|           TopkFeatureTEST                   |                   ```python      models.util.exam_selectKbest     ```              |


## Model Selector: 
Model Selector handles the selection of a model. That is, users of this toolkit can choose from any of the four traditional ML models and the five DL models used in this study. We also note that a new model can be easily added and testified under the current framework. After a model is selected, the users can either directly adopt the model parameters derived from our evaluation or training the model from scratch, e.g., using grid search to fine- tune a traditional ML model or coupling BERT with a DL model for co-training.

* initialise base classifier:
```python
classifier = ml_clf(config)
```

* create a Naive bayes classifier: 
```python
classifier.nb_clf()
```

* create a SVM classifier:
```python
classifier.svm_clf()
```

* create a Logistic regression classifier:
```python
classifier.lr_clf()
```

* create a Random forest classifier:
```python
classifier.rf_clf()
```

* to perform a simple grid search with pre-defined parameters:

```python
grid = GridSearchCV(YOUR_MODEL,YOUR_SEARCH_PARAMS,refit=True,verbose=2)
grid.fit(self.Train_X,self.Train_Y)
print(grid.best_estimator_)
```

* to run a model with hyperparamter, replace model function and add parameter: 
e.g., 
replace: 
```python
rfc=RandomForestClassifier()
```

with: 
```python
rfc=RandomForestClassifier(bootstrap=True, class_weight=None, criterion='gini',
  max_depth=None, max_features=0.5, max_leaf_nodes=None,
  min_impurity_split=1e-07, min_samples_leaf=1,
  min_samples_split=4, min_weight_fraction_leaf=0.0,
  n_estimators=250, n_jobs=1, oob_score=False, random_state=None,
  verbose=0, warm_start=False)
```

We used a service called [Bert-as-a-service](https://github.com/hanxiao/bert-as-service) to generate BERT embeddings of the forum post. 
The embedding is then used as input for DL models

We refer this repo: [TextClassification](https://github.com/zackhy/TextClassification), where DL code was modified from. 

|               Function                |                                   Example                                                 |
| :-----------------------------------: | :---------------------------------------------------------------------------------------: |
|            Naive bayes                   |                ```python ml_classifiers.nb_clf    ```                                |
|            Logistic regression                   |                     ```python      ml_classifiers.lr_clf    ```            |
|     Random forest     |                    ```python          ml_classifiers.rf_clf ```                |
|           Support vector machine                   |                   ```python    ml_classifiers.svm_clf     ```              |
|           CLSTM                  |                   ```python      clstm_classifier    ```              |
|          BLSTM                   |                   ```python      rnn_classifier     ```              |

## Performance Evaluator: 
Performance Evaluator is responsible for calculat- ing the classification performance of a model in terms of the following four metrics: Accuracy, Cohen’s κ, AUC, and F1 score.


|               Function                |                                   Example                                                 |
| :-----------------------------------: | :---------------------------------------------------------------------------------------: |
|            Accuracy                   |                ```python sklearn.metrics.accuracy_score    ```                                |
|           cohen's Kappa                |                     ```python      sklearn.metrics.cohen_kappa_score    ```            |
|     AUC     |                    ```python          sklearn.metrics.roc_auc_score ```                |
|           F1                   |                   ```python    sklearn.metrics.f1_score     ```              |


