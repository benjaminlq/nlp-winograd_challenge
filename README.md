# <center> NLP Winograd Challenge <center/>
### <center> Winograd Challenge using pretrained Roberta transformer-based model.
<br><br>

## Background/Problem Description
The Winograd Schema Challenge was introduced for testing AI agents level of understanding for commonsense knowledge proposed by Hector Levesque, designed to be an alternative to Turing Test.

Definition from [Wikipedia](https://en.wikipedia.org/wiki/Winograd_schema_challenge):

A Winograd schema challenge question consists of three parts:  
1. A sentence or brief discourse that contains the following:

    * Two noun phrases of the same semantic class (male, female, inanimate, or group of objects or people)
    * An ambiguous pronoun that may refer to either of the above noun phrases
    * A special word and alternate word, such that if the special word is replaced with the alternate word, the natural resolution of the pronoun changes.
2. A question asking the identity of the ambiguous pronoun, and
3. Two answer choices corresponding to the noun phrases in question.

**Example 1**: The plant took up too much room in the urn, because the _ was small. 
1. plant    
2. urn

    _Answer_ : 2
<br><br>

## Dataset
The dataset contains a train set (8966 questions), a dev set (1267 questions) and a test set (1267 questions). The train set was used for training, the dev set was used for hyperparameter tuning and the test set is used for final model evaluation.
<br><br>

## Methodology
The approach used to tackle this problem is convert it into a classification problem in which the respective probability values of observing the sentence when the blank is replaced by each candidate are computed. For the example in the description, this means to construct 2 sentences by filling in the blanks. This means to maximise the probability function:

$$ Sentence = argmax_{Sentence} P(Candidate Sentence | Initial Sentence) $$
* Initial Sentence:<br>
The plant took up too much room in the urn, because the _ was small.<br><br>
    * Candidate Sentence 1: <br>
The plant took up too much room in the urn, because the `urn` was small. <br><br>
    * Candidate Sentence 2: <br>
The plant took up too much room in the urn, because the `plant` was small. <br><br>

The sentence chosen is the one which gives the higher probability P(Candidate Sentence | Initial Sentence). 

To do this, each candidate sentence is broken down into two sub-sentences to predict if the later portion is likely to be the next sub-sentence of the initial part. The blank token to be filled in is the start of the second half. <br><br>
For example for Candidate Sentence 1: <br>

* First Part (Context): The plant took up too much room in the urn, because the <Br>
* Second Part (Prediction): `urn` was small.<br>

* Label (Ground Truth): 1 (True) <br><br>

## Model Selection - RoBERTa
RobertaForSequenceClassification was used to train and predict the probability of the two candidate sequence and pick the better candidate. For Candidate 1, the input token sequence is:<br>

'[CLS] The plant took up too much room in the urn, because the [SEP][SEP] urn was small.[SEP]' <br>
where [CLS] is start token, [SEP] is end token and [SEP][SEP] is the tokens between the two sub-sentences.<br><br>

## Results & Conclusion
The RobertaForSequenceClassification fine-tuned model achieve accuracy of 70.5% on test set. 
Some degree of overfitting observed between training and testing dataset, as cross-validation accuracy is ~78%.

## References
https://en.wikipedia.org/wiki/Winograd_schema_challenge <br>
https://www.sciencedirect.com/science/article/pii/S0004370214000356?via%3Dihub
