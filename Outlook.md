# The general outlook of the database.
First, some necessary imports:
```
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline #sinec I am working in Jupyter environment.
```

### Sampling
I can sample the database for faster processing with the following code. I am chosing a 1/1000 sample. 
```
import random
p = 0.001  

data = pd.read_csv(r"C:\Users\[file path]\all_data3_noText2.Practice2.csv", 
                 index_col=0,
                 header=0,
                 skiprows=lambda i: i>0 and random.random() > p)
df=pd.DataFrame(data)
````
**Note: Below operations are based on the full scale database not the sample, unless otherwise stated.** 


## Distribution of data based on languages:

```
df.Language.value_counts().head(20)
```
Out:

![languages](https://user-images.githubusercontent.com/80383987/163654396-c1995077-ad7e-4994-84ce-010568dbf338.PNG)

We can draw a barchart of top ten languages:
```
sns.barplot(x= df.Language.value_counts()[:10].index,y=df.Language.value_counts()[:10])
```
![language barchart](https://user-images.githubusercontent.com/80383987/163654450-cfb2bae3-3f02-48f9-b969-e7005bb7afa0.png)

For more detailed info about our language, I am grouping the whole databse by language.
```
df.groupby('Language')
```
And use .describe to get detailed informaton about each language.
```
df.groupby('Language').describe()
````
Here is what I will get:
![lang desribe](https://user-images.githubusercontent.com/80383987/163654537-f7543fc4-3b5d-4056-a457-1d659b62e110.PNG)

Alternatively, we can only chose what statistical measures we need. Here I just want to know the count, minimum and maximum of followers for each language.
```
df.groupby('Language').Followers.agg(['count', 'min', 'max'])
```
Or grouping it based on the year of creation of account and finding the means for each column:
```
df.groupby('Year Account Created').mean()
```
output (for the sample):

![fo mea](https://user-images.githubusercontent.com/80383987/163656331-214dc1f1-3083-4879-b6fc-7a5a2de5e9ad.PNG)


In some tables one can observe that numbers are in scientific notation. We can change it to more readable format, not neccessary though. 
### Suppresing Scientific numbers to readable format
Use this code:

```
pd.set_option('display.float_format', lambda x: '%.3f' % x)  
df.head(10)
```
We can reset to normal with:
```
pd.reset_option('display.float_format')
```


This is too messy I assume. So, why not see only top tens:
```
lang.sort_values(by=('userid', 'count'), ascending=False).head(10)
```
![lang top ten descr](https://user-images.githubusercontent.com/80383987/163654739-b69b875c-cefd-4ff5-8c14-bd31d3b54b91.PNG)

Yes, much cleaner! Later I will explain the meaning of each of these statistical functions and how they help us to make sense of our data. 
Sometimes, transposing groupby tables are easier to process. 
### Transpose
```
lang.sort_values(by=('userid', 'count'), ascending=False).head(10).transpose()
```
![transpose](https://user-images.githubusercontent.com/80383987/163654834-de78f42b-1fb5-4d64-b317-7e70622b7a1a.PNG)

## Distribution of data based on the year of creation of the account
First lets extract only year from dates:
```
df['Year Account Created'] = pd.DatetimeIndex(df['Account Creation Date']).year

```
Let's see the distribution of records (tweets) based on the year of creation of users:
```
df['Year Account Created'].value_counts().head(20)
```
Out:
![year top](https://user-images.githubusercontent.com/80383987/163655037-2648303e-b81f-48af-807c-3801a1caadf6.PNG)

And visualize it:
```
sns.barplot(x= df['Year Account Created'].value_counts()[:20].index,y=df['Year Account Created'].value_counts()[:20])
```
out:

![year chart](https://user-images.githubusercontent.com/80383987/163655081-be6b9196-23af-4b40-acf3-8f8442900ff6.png)


Then we can get detailed informaton by grouping by year:
```
df.groupby('Year Account Created').describe()
```
out:

![groupby year](https://user-images.githubusercontent.com/80383987/163654973-29dd2760-470f-4feb-9703-0d85546ccf68.PNG)

### Twitter Accounts Created in 1970? 
Something strange happened. Let's see how many records we have with year 1970 and then drop them. The numbers are only a few, so, not meaningful for our large (20 million tweet database)

```
df[df['Year Account Created']==1970].count()
df.drop(df[(df['Year Account Created']==1970)].index, inplace=True)
```
Here is the description of our dataframe grouped by year of creation of account:

![year 3](https://user-images.githubusercontent.com/80383987/163655198-5dae81b7-36af-4f60-a2df-0c54d5340554.PNG)

we can also write this to a new csv file for further anlalysis if we like.

## USERS

Number of unique USERS:  
```
df['User ID'].nunique()
```
Out: 3478339
Number of unique tweets: `df['Tweet ID'].nunique()` and out is: 20314584. 

### Users with the most tweets:
```
df['User ID'].value_counts().head(50)
```
out:

![topusers](https://user-images.githubusercontent.com/80383987/163655434-2ce243b2-ea0b-4208-9434-add222863ef6.PNG)

As one can see user:1499763123603050497 (@FuckPutinBot) has tweeted more than 18,000 tweets during our timeframe. Later, I will explain in more detail how to make sense of this list and to extract insights from it. But for now, we can convert the ids to usernames be calling our original dataframe. 

### Sort dataframe based on different columns or slice:

Here the dataframe sorted based on retweet count:

```
df.sort_values(by='Retweet Count', ascending=False)
```
out:

![sort1](https://user-images.githubusercontent.com/80383987/163655641-577b6ddf-0339-4629-883c-dc35d4b753b9.PNG)

And let's make a dataframe specifically for top users: those with  more than 50,000 followers.
```
df[df['Followers']>50000]
```
out:

![sort2](https://user-images.githubusercontent.com/80383987/163655708-fb21f829-74a0-4190-8ae5-eafd442a5880.PNG)

We can do the same with other columns.

## Let's find how many accounts in each language are created in each year:

```
df['COUNTER'] =1    
group_data = df.groupby(['Language','Year Account Created'])['COUNTER'].sum() 
group_data.sort_values(ascending=False)
```
Out:

![lang peryear](https://user-images.githubusercontent.com/80383987/163655757-cd9e8390-4512-4d1c-b43b-e747c21e8373.PNG)

It shows for example, that 1636745 accounts in English are created in 2021, and so on.

We can convert it to a dataframe `group_data=pd.DataFrame(group_data)` and then use cross tabulation to have a better look.

### Cross Tabulation 

```
pd.crosstab(df.Language, df.Year)
```

out:
![crosstab](https://user-images.githubusercontent.com/80383987/163655831-f3457bb2-68a3-4784-9a52-cd46f3dbebf2.PNG)

## Other ways to make sense of our data: Converting continuous data into categorical data
This will especially help us in visulization. We can do many of these processings with BI or other tools too. 
Here I am creating a categories of accounts based on the number of their followers.

```
pd.cut(df.Followers, bins=[0,1, 10, 50, 100, 500, 1000, 10000,100000,1000000, 10000000], labels=['zero', 'ones', 'tens', 'fiftys', 'hundreds', 'k', 'tenk', 'hunk','mil', 'tenmil' ]).value_counts()
```
out:
![categorize data](https://user-images.githubusercontent.com/80383987/163655915-99a856f1-6952-40d7-b3f0-db0408b28cf4.PNG)

I am doing the same for Following and turn it into a Pandas DataFrame:

```
fol=pd.cut(df.Following, bins=[0,1, 10, 50, 100, 500, 1000, 10000,100000,1000000, 10000000], 
       labels=['zero', 'ones', 'tens', 'fiftys', 'hundreds', 'k', 'tenk', 'hunk','mil', 'tenmil' ]).value_counts()
fol=pd.DataFrame(fol)
fol
```
Here is the result:

![fol](https://user-images.githubusercontent.com/80383987/163655999-7e043477-f306-4506-aa2c-f5b2366f8119.PNG)


