#Sentiment Analysis
##Problem Description
Predict a rating of product review. The ratings are 1,2,3,4,5. This is an ordinal regression problem, that can also be solved as regression problem and as multiclass classification problem.
 
##Data
Reviews of books in Amazon, scrapped from Amazon site by UPenn researchers ([http://www.cs.jhu.edu/~mdredze/datasets/sentiment/](http://www.cs.jhu.edu/~mdredze/datasets/sentiment/)). The original dataset has 975K reviews with rankings 1,2,3,4,5. To speed up the experiment we down-sampled the dataset to 10K reviews. All reviews are in English. The reviews were written in 1997-2007. 
 
##Model
We used feature hashing module to transform English text to integer-valued features. We compared three models:  
 
1. linear regression   
2. ordinal regression using two-class logistic regression as a base classifier
3. multiclass classification using multiclass logistic regression classifier
 
##Results
 
Algorithm|                         Mean Absolute Error|           Root Mean Square Error
---------|  
Ordinal Regression |             0.82|                                            1.41|
Linear Regression  |              1.04|                                            1.36|
Multiclass classification  |    0.85  |                                          1.57
 
Based on these results we chose ordinal regression model and build a web service based on it.
 
##API
We have built a web service that takes a plain text review and predicts its rating.
