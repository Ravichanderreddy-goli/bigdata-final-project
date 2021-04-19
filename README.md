# bigdata-final-project
## Ravichander Reddy Goli - Author

## Text data
Source [The Project Gutenberg eBook of Keetje, by Neel Doff](https://www.gutenberg.org/files/65096/65096-0.txt)

- For this project, I have taken a book from project gutenberg, I have used the text gutenberg ebook of Keetje, by Neel Doff using pySpark, Firstly I am going to pull the data, Then after pulling I will be cleaning the data then I will be processing the data, then at last I will be graphing the most commonly used words.

## Tools/Languages
- Languages: Python
- Tools: Pyspark, Databricks, Notebooks, pandas, regex, matplotlib, urllib.

## Databrick community
Here we will be using Databrick community for running the commands. First we have to create a cluster, then after creating it we have to go to the notebooks and create a new notebook with language selected as python.Then after that we have to enter the commands and run it.

## Commands:
## Pulling the data
```
import urllib.request
urllib.request.urlretrieve("https://raw.githubusercontent.com/Ravichanderreddy-goli/bigdata-final-project/main/Project%20GutenbergeBookofKeetje.txt", "/tmp/ravi.txt")
```
In this we are pulling the data and store it in a tmp folder /ravi.txt

```
dbutils.fs.mv("file:/tmp/ravi.txt", "dbfs:/data/ravi.txt")
```
In this first argument will start with a file, then the location. In Second argument we store the file in data folder and it starts with dbfs.

```
raviRDD = sc.textFile("dbfs:/data/ravi.txt")
```
In the last step of first process is moving the file into spark. We should use the new file location when we enter.

## Cleaning the data
```
wordsRDD=raviRDD.flatMap(lambda line : line.lower().strip().split(" "))
```
The data file which was choosen has Capitalization, punctuation and stop words. For this we will use the flat map and break the data where we will change the capital words to smaller words, remove the spaces and splitting the sentences into words.

```
import re
cleanTokensRDD = wordsRDD.map(lambda w: re.sub(r'[^a-zA-Z]','',w))
```
In this step we will remove all the punctuation. For this we will use a regular expression, for that we need to import library re.

```
from pyspark.ml.feature import StopWordsRemover
remover =StopWordsRemover()
stopwords = remover.getStopWords()
cleanwordRDD=cleanTokensRDD.filter(lambda w: w not in stopwords)
```
In this step we will import stopwwordsremover to remove the stop words. Then we will filter out the words from the library.

## Process the data
```
IKVPairsRDD= cleanwordRDD.map(lambda word: (word,1))
```
In processing we need to change the words to (word,1) format. We have to map words to key value pairs.

```
wordCountRDD = IKVPairsRDD.reduceByKey(lambda acc, value: acc+value)
```
In this we will use reduce by key word. We will keep the words that appear. We will remove the word if it appears again and add the first words count.

```
res = wordCountRDD.collect()
```
Here we use collect function. Here we will return to python for more complicated processing.

## Finding the data
```
res.sort(key=lambda x:x[1])
res.reverse()
print(res[:11])
```
Here to find the most common words we sort out the list for the common words. It will sort from low to high so we will have the most used words.I am printing the top 11 words.

```
mostCommon=res[1:11]
word,count = zip(*mostCommon)
import matplotlib.pyplot as plt
fig = plt.figure()
plt.bar(word,count,color = "Red")
plt.xlabel("Most used words")
plt.ylabel("Number of times it is used")
plt.title("Most used words in gutenberg ebook of keetje")
plt.show()
```
We will use the library matplotlib. I am plotting the graph with the most used words in x-axis, number of times it is used in y-axis. I have used the charting as bar graph.

## Results
![](https://github.com/Ravichanderreddy-goli/bigdata-final-project/blob/main/results.JPG)

## Chart
![](https://github.com/Ravichanderreddy-goli/bigdata-final-project/blob/main/chart.JPG)

## References
[DZone](https://dzone.com/articles/types-of-matplotlib-in-python)
[Python](https://docs.python.org/3/library/re.html)
