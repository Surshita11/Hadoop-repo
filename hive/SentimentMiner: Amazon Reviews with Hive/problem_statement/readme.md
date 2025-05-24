
Problem Statement:
Imagine you work for an e-commerce website as a Big Data analyst. Millions of people put reviews on the products on your website. You have been tasked with
running an analysis on these reviews.

Datasets are attached -
1. senticnet4.txt: for word sentiment
2. amazondataset.csv: I was unable to attach the csv file because it was too large file. Instead, I have attached images of the csv file. These image show that each column with its contents. The data is of total 24 columns.

Tasks To Be Performed:
1. Analyze all the positive reviews (any review with a rating of 4 and above is
considered positive) and find out the top 20 words used in those positive
reviews.
2. Use the word sentiment dataset and find out the percentage of words that
are positive, negative and neutral. The words that aren’t mentioned in the
word sentiment dataset are considered as neutral.

Note: The attributes, ‘reviewsrating’ and ‘reviewstext’ correspond with the ratings
and the text reviews, respectively.
Note: In the word sentiment dataset, a positive numeric value denotes a positive
emotion and a negative numeric value denotes a negative emotion attached to
the word.

Constraints:
● For accuracy, do not treat uppercase words and lowercase words as
separate words. Convert all words to lowercase. (For example: ‘His’ and
‘his’ should not be treated as separate words)
If you have a sentence “His his”, then the output should be:
his 2
And not:
his 1
His 1
● Remove all punctuation marks from the beginning and the end of all words:
‘hello?’, ‘hello.’, ‘hello-‘, ‘hello’, ‘HellO’
will all correspond to the following count:
hello 5
