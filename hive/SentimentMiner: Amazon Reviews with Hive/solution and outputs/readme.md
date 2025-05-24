Step 1: Load and Create Tables
1.1 For Amazon Reviews Table - 

CREATE EXTERNAL TABLE amazon_reviews (
  id STRING,
  dateAdded STRING,
  dateUpdated STRING,
  name STRING,
  asins STRING,
  brand STRING,
  categories STRING,
  primaryCategories STRING,
  imageURLs STRING,
  keys STRING,
  manufacturer STRING,
  manufactuerNumber STRING,
  reviewsdate STRING,
  reviewsdateSeen STRING,
  reviewsdidPurchase STRING,
  reviewsdoRecommend STRING,
  reviewsid STRING,
  reviewsnumHelpful INT,
  reviewsrating INT,
  reviewssourceURLs STRING,
  reviewstext STRING,
  reviewstitle STRING,
  reviewsusername STRING,
  sourceURLs STRING
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hive/warehouse/amazondataset.csv';


1.2 SenticNet Table -

CREATE EXTERNAL TABLE senticnet (
  word STRING,
  sentiment STRING,
  score FLOAT
)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE
LOCATION '/user/hive/warehouse/senticnet4.txt';


Step 2: Extract Positive Reviews and Tokenize
2.1. Select Positive Reviews (rating >= 4)
CREATE TABLE positive_reviews AS
SELECT reviewstext
FROM amazon_reviews
WHERE reviewsrating >= 4 AND reviewstext IS NOT NULL;


2.2. Tokenize, Lowercase, and Clean Words
CREATE TABLE positive_words AS
SELECT
  LOWER(REGEXP_REPLACE(word, '^[^a-zA-Z0-9]+|[^a-zA-Z0-9]+$', '')) AS clean_word
FROM positive_reviews
LATERAL VIEW explode(split(reviewstext, ' ')) t as word
WHERE LENGTH(word) > 0;


Step 3: Find Top 20 Words in Positive Reviews
SELECT clean_word, COUNT(*) as cnt
FROM positive_words
WHERE clean_word != '' -- ignore empty strings
GROUP BY clean_word
ORDER BY cnt DESC
LIMIT 20;


Step 4: Word Sentiment Analysis
4.1. Join Words with SenticNet
CREATE TABLE positive_words_sentiment AS
SELECT
  w.clean_word,
  CASE
    WHEN s.sentiment = 'positive' THEN 'positive'
    WHEN s.sentiment = 'negative' THEN 'negative'
    ELSE 'neutral'
  END AS word_sentiment
FROM positive_words w
LEFT JOIN senticnet s
  ON w.clean_word = s.word;


4.2. Calculate Sentiment Percentages
SELECT
  word_sentiment,
  COUNT(*) AS word_count,
  ROUND(100.0 * COUNT(*) / (SELECT COUNT(*) FROM positive_words_sentiment), 2) AS percentage
FROM positive_words_sentiment
GROUP BY word_sentiment;
