# NAIVE BAYES IMPLEMENTATION FOR TEXT CLASSIFICATION

## STRUCTURE
The project is divided into four submodules under main:
    - naive_bayes_classifier (USE THIS TO GET FINAL PREDICTIONS, INSTRUCTIONS BELOW)
    - random_classifier
    - naive_bayes_classifier_with_validation
    - resources

Each module has its corresponding test_module under the folder src/test except for the resources module.

## NAIVE BAYES CLASSIFIER

### module naive_bayes_classifier

THIS IS THE CLASS TO EXECUTE IN ORDER TO GET THE PREDICTIONS SUBMITTED ON KAGGLE, ALPHA=0.59
SET ALPHA TO 0.0 TO REMOVE SMOOTHING

### IMPLEMENTATION

This is the naive bayes implementation of our classifier. It takes the train data as a 2d numpy array (examples * label)
created using data_import (explained at the end of the readme) and an alpha parameter corresponding to the smoothing value.
The following explains how the code is organized:

    1. Training phase
        a. Splits the train data into an array of nested arrays organized by labels.
        b. Compute the priors
        c. Cleans the data (punctuation, lowers words)
        d. Defines the vocabulary using clean data
        e. Creates list of dictionaries each keeping count of word occurences for each label

    2. Prediction phase
        a. Receives test data
        b. For each test example:
            i. Cleans the words using the same criteria as for training data (normalization)
            ii. Gets index of most likely label by calling get_index_of_most_likely_label()
                    - Computes log likelihoods for each label and returns index of highest likelihood
        c. Stores predictions in list
        d. Returns predictions

    3. Helper methods
        For readability concerns, the code was split into multiple methods with method names that are self explanatory.
        A lot of tasks, such as cleaning the data (removing punctuation, tokenizing), initializing lists were split
        in different methods.

## HOW TO RUN

RUN THE TEST ALREADY WRITTEN UNDER src/test/naive_bayes_classifier/test_naive_bayes_classifier.py
REMOVE THE @unittest.skip FROM THE TEST_SAVE_PREDICTIONS TEST AND EXECUTE THE TEST.

OR

IMPORTS NEEDED WHEN RUNNING FROM OTHER .py FILE
from src.main.naive_bayes_classifier.naive_bayes_classifier import *
from src.main.data_import import *

CODE TO EXECUTE FROM OTHER .py FILE
        data_import = DataImport()
        data = data_import.get_clean_data_set_as_array(data_import.get_train_data_as_tuple())
        classifier = NaiveBayesClassifier(data, alpha) #alpha is optional
        test_data = data_import.get_test_data_as_list()
        classifier.train()
        predictions = classifier.predict(test_data)
        classifier.save_predictions(np.array(predictions)) # will save the predictions in current directory


## NAIVE BAYES CLASSIFIER WITH VALIDATION

### module = naive_bayes_classifier_with_validation

Tests the trained classifier using the validation set and returns the error rate.
This class extends naive_bayes_classifier and splits the train data in train and validation set. The training set is executed on 98%
of the initial train data provided and the remaining examples are used as test data to get a sense of the accuracy of our classifier.
In order, to change the proportion of train and test data, simply change the value of the percentage variable in
the split_train_validation_data() method.

### HOW TO RUN

Sample code to run it is as follows:
        data_import = DataImport()
        data = get_clean_data_set_as_array(data_import.get_train_data_as_tuple())
        classifier = NaiveBayesClassifierWithValidation(data, 0.59)
        error_rate = classifier.train()

OR

Remove the @unittest.skip annotation from the test_train in the test_naive_bayes_classifier_with_validation
and execute the test.

## RANDOM CLASSIFIER

### module = random_classifier

This is a simple classifier that takes train data during instantiation of the class to find the unique existing labels.
It has a "predict" method implemented that takes the test data and randomly selects a label from the list of existing labels
for each test example. It returns the prediction as a list.
It also has a "predict_weighted" method that randomly selects from a list of weighted labels (improves accuracy).

### HOW TO RUN

Here's a sample code to run it:

        data_import = DataImport()
        clean_train_data = data_import.get_clean_data_set_as_array(data_import.get_train_data_as_tuple())
        classifier = RandomClassifier(clean_train_data)
        predictions = classifier.predict_weighted(data_import.get_test_data_as_list())
        classifier.save_predictions(predictions) #will store predictions as a csv in current directory
        
or

You can also go in the test_random_classifier.py under src/test/random_classifier/test_random_classifier.py and
remove the @unittest.skip from the test save_predictions.

## DATA IMPORT

### module = data_import.py

This python class is used to fetch the data and return it in a processable form for the classifiers.
