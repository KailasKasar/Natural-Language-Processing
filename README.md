# Natural-Language-Processing
Natural Language Processing (or NLP) is applying Machine Learning algorithms to words and phrases.
Using Natural Language Processing we are teaching machines to understand what is said in spoken and written word. 

#Wikipedia definition for NLP

Natural language processing (NLP) is a subfield of computer science, information engineering, 
and artificial intelligence concerned with the interactions between computers and human (natural) languages,
in particular how to program computers to process and analyze large amounts of natural language data.

In this project I am using NLP algorithm to classify dataset SMS message as spam or not spam.

Machine Learning models like Logistic Regression, Naive Bayes, CART are best used for classification problem.

#Naive Bayes
I am using Naive Bayes model that can classify dataset SMS messages as spam or not spam.
Dataset used for this project is available on UCI machine learning repository https://archive.ics.uci.edu/ml/datasets/SMS+Spam+Collection.
Bayes theorem calculates the probability of a certain event happening(message being spam)
based on the joint probabilistic distributions of certain other events(a message being classified as spam).

Will follow below steps to build the classification model.

1) Clean the dataset
2) Create bag of word model
3) Apply Naive bayes model onto bag of word using scikit-learn.

# Importing the pandas libraries
import pandas as pd

# Importing the dataset
dataset = pd.read_table('SMSSpamCollection', sep='\t', 
                   header=None, 
                   names=['label', 'sms_message'])

# As scikit-learn deals with binary value we need to convert our label into binary value.
dataset['label'] = dataset.label.map({'ham':0, 'spam':1})

# Cleaning the texts
# To process the raw text we use re (for regular expression) and nltk libraries.
#Steps to clean the text are
#1) finding non albhabets in dataset
#2) convering albhabets in lower case
#3) spliting the string into words
#4) finding non english words in array

import re
from nltk.corpus import stopwords
from nltk.stem.porter import PorterStemmer
bag = []
for i in range(0, 5572):
    msg = re.sub('[^a-zA-Z]', ' ', dataset['sms_message'][i])
    msg = msg.lower()
    msg = msg.split()
    ps = PorterStemmer()
    msg = [ps.stem(word) for word in msg if not word in set(stopwords.words('english'))]
    msg = ' '.join(msg)
    bag.append(msg)

# Creating the Bag of Words model
#A bag-of-words model, or BoW for short, is a way of extracting features from text for use in modeling, such as with machine learning algorithms.
#The approach is very simple and flexible, and can be used in a myriad of ways for extracting features from documents.
#A bag-of-words is a representation of text that describes the occurrence of words within a document.
#It involves two things:
#A vocabulary of known words.
#A measure of the presence of known words.
#reference:https://machinelearningmastery.com/gentle-introduction-bag-words-model/

from sklearn.feature_extraction.text import CountVectorizer
# CountVectorizer convert a collection of text documents to a matrix of token counts
cv = CountVectorizer(max_features = 5000)
X = cv.fit_transform(bag).toarray()
y = dataset['label'].values

# Splitting the dataset into the Training set and Test set
from sklearn.cross_validation import train_test_split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.20, random_state = 0)

# Fitting Naive Bayes to the Training set

from sklearn.naive_bayes import MultinomialNB
classifier = MultinomialNB()
classifier.fit(X_train, y_train)

# Predicting the Test set results
y_pred = classifier.predict(X_test)

# Making the Confusion Matrix
from sklearn.metrics import confusion_matrix
cm = confusion_matrix(y_test, y_pred)

#Confusion matrix is showing great accuracy for developed model.
  |  0  | 1
-------------
0 | 946 | 9
------------
1 | 8   | 152

#Advantages of using Naive bayes
Naive bayes approch is simple and fast and have ability to handle large numer of features.
Naive bayes rarely overfit the data.
In our model it has handle large nummber of features(5000), 
in presence of irrelevant features model performance is unaffected and produce accurate predictions.

