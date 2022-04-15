# About TweetDataBase
TweetDataBase is a collection of 20 million unique tweets about Ukraine collected daily for 47 days. 
The source of our data is here: https://www.kaggle.com/datasets/bwandowando/ukraine-russian-crisis-twitter-dataset-1-2-m-rows
There are 47 .gzip files each for one day. 
## Importing all databases and creating a one big dataframe for all. 
First let's import necessary libraries.
```
import pandas as pd
import numpy as np
import os
```
Then we need to create a dataframe from one file to add other files to it later.
```
df=pd.read_csv(r"C:\Users\[filepath]TWWETALL\0401_UkraineCombinedTweetsDeduped.csv.gzip", compression = 'gzip', index_col=0)
```
Now let's iterate through all 47 files in the folder.
```
files=[file for file in os.listdir(r"C:/Users/[folder path]/TWWETALL/")]
print(files)
```
out:
>['0401_UkraineCombinedTweetsDeduped.csv.gzip', '0402_UkraineCombinedTweetsDeduped.csv.gzip', '0403_UkraineCombinedTweetsDeduped.csv.gzip', '0404_UkraineCombinedTweetsDeduped.csv.gzip', '0405_UkraineCombinedTweetsDeduped.csv.gzip', '0406_UkraineCombinedTweetsDeduped.csv.gzip', '0407_UkraineCombinedTweetsDeduped.csv.gzip', '0408_UkraineCombinedTweetsDeduped.csv.gzip', '0409_UkraineCombinedTweetsDeduped.csv.gzip', '0410_UkraineCombinedTweetsDeduped.csv.gzip', '0411_UkraineCombinedTweetsDeduped.csv.gzip', '0412_UkraineCombinedTweetsDeduped.csv.gzip', 'UkraineCombinedTweetsDeduped20220227-131611.csv.gzip', 'UkraineCombinedTweetsDeduped_FEB27.csv.gzip', 'UkraineCombinedTweetsDeduped_FEB28_part1.csv.gzip', 'UkraineCombinedTweetsDeduped_FEB28_part2.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR01.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR02.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR03.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR04.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR05.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR06.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR07.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR08.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR09.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR10.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR11.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR12.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR13.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR14.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR15.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR16.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR17.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR18.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR19.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR20.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR21.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR22.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR23.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR24.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR25.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR26.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR27_to_28.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR29.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR30_REAL.csv.gzip', 'UkraineCombinedTweetsDeduped_MAR31.csv.gzip']

### Concatenate files together
```
for file in files:
    df=pd.read_csv(r"C:/Users/mq20206390/Downloads/TWWETALL/"+ file, compression = "gzip" , index_col=0)
all_data.head()
```
You will get a dataframe with the following columns:
>userid	username	acctdesc	location	following	followers	totaltweets	usercreatedts	tweetid	tweetcreatedts	retweetcount	text	hashtags	language	coordinates	favorite_count	extractedts

Then I wrrote this main dataframe to a csv file, using utf-8 encoding. 
I need to clean up it, by renaming columns to readable ones, and then deleting columns that we do not need at the moment. 

```

df.rename({'userid': 'User ID', 'username': 'User Name', 'acctdesc': 'Account Description', 'location': 'Location', 'following': 'Following', 'followers': 'Followers', 'totaltweets': 'Total Tweets', 'usercreatedts': 'Account Creation Date', 'tweetid': 'Tweet ID', 'tweetcreatedts': 'DateTime Tweet Created','retweetcount': 'Retweet Count', 'text': 'Text', 'hashtags': 'Hashtags', 'language': 'Language'}, axis=1, inplace=True)
df.drop(['coordinates', 'favorite_count', 'extractedts'], axis=1, inplace=True) 
df.head()
```
Let's also change datatype of "Account Creation Date" and "DateTime Tweet Created" to datetime.
```
df['Account Creation Date'] =  pd.to_datetime(df['Account Creation Date'], infer_datetime_format=True)
```
We will get these headers:
> User Name	Account Description	Location	Following	Followers	Total Tweets	Account Creation Date	Tweet ID	DateTime Tweet Created	Retweet Count	Text	Hashtags	Language

### Delete rows containing strings for faster processing.
At this stage, I just want to do some statistical analyses, so, I am deleting string columns to make processing faster. 
```
df.drop(['User Name', 'Account Description', 'Location','Text','Hashtags'], axis=1, inplace=True)
df.head()
```
New headers:
>Following	Followers	Total Tweets	Account Creation Date	Tweet ID	Date of Tweet	Retweet Count	Language

We can write this new no-text dataframe to a new csv file:
```
df.to_csv(r"C:\Users\[filepath]all_data3_noText.csv", encoding='utf-8')
```


