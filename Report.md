# 

# Assignment 5 Midterm Project Report

# Data
The sample being focused on in this midterm is the S&P 500 Data from 2022. More specifically, we are focusing on extracting each firms' 10K html file to get: 
- CIKs
- Accession Numbers
- Filing Dates
- Returns
- Buy and Hold Returns

## My approach on building and modifying the return data: 
1. I began with unzipping the 10K files folder with this function: ```with ZipFile('10k_files/10k_files.zip', 'r') as zipfolder: ```
2. For the Accession Numbers, I used the ```.namelist()``` function to extract the names of the folders within the 10k files folder. I appended these folder names to a list, and then I appended the list to the dataframe as a new column. 
3. Then, I need to get the filing dates. I accomplished this by:
    - creating an HTML session and setting the "url" variable to this:``` url = f'https://www.sec.gov/Archives/edgar/data/{cik}/{accession_number}-index.html'```
    - To get the filing dates, I used this line of code to extract the dates: ```filing_date = r.html.find('div.info', first=True).text if r.html.find('div.info', first=True) else "Filing date not found!"```
    - It is important to note the use of this code: ```time.sleep(0.05)``` to slow down my requests from the SEC website to prevent a temporary ban. 
4. For the Returns, I extracted the data from the github link that goes to "crsp_2022_only.zip". I downloaded the zip file and moved it into a folder in my repository called "returns" (similar to the '10k_files' folder). I used this method because I first tried the ' url = ... ' method, however, I could not get it to work. 
    - I acquired the returns by using a similar function as the one I used in step 1 above: 
    ```with ZipFile('returns/crsp_2022_only.zip', 'r') as archive:
    stata_file_name = archive.namelist()[0]
    with archive.open(stata_file_name) as stata_file:
    crsp = pd.read_stata(stata_file)```

    - Then, I merged the original sp500 dataframe with my modified dataframe, df_sp500, which contains the ML/LM Positive and Negative Variables. I named this new dataframe "analysis_df"
    
    - Following this, I merged "analysis_df" with the crsp data (return data) by using this merge: 
```analysis_df.rename(columns={'Symbol_x': 'ticker'}, inplace=True)
return_merge = pd.merge(analysis_df, crsp, on = 'ticker', how = 'inner')```

    - For that final merge, I changed the column name in "analysis_df" for the firm symbols to 'ticker' so that I could merge with 'ticker' in the crsp.zip file
    
5. For the Buy/Hold Returns, I grouped the data in the CRSP dataframe by 'ticker'. Then, I created a dictionary to store the buy and hold returns that I calculated within the for loop that I used: ```for ticker, group in grouped_crsp:```
    - I used this line of code to do the calculation: ```buy_hold_return = group_sort['ret'].iloc[-1] - group_sort['ret'].iloc[0]```
    - I then converted the list of dictionaries into a dataframe, which was outputted into the 'output' folder as a file named 'buy and hold returns.csv'

# Issues with the return merge
###  **The final merged dataframe is called 'final_merged_data.csv' in the 'output' folder**
### I gitignored the crsp_2022_only.zip file. However, I could not size down the resulting dataframe. I should only have around 503 rows of data, but the resulting dataframe inlcuded the rest of the data from crsp_2022_only.zip for the returns. That is one of the issues there is with this. 
    

# Sentiment Variables
I loaded the ML Positive/Negative and LM Positive/Negative into their respective lists. I would like to note that for the filtering of LM_MasterDictionary_1993-2021.csv file, I filtered the positive and negative LMs based on where '2009' was present. I made this assumption because 2009 was only present on either the positive or negative columns, so I made a function that assigned the word in the row to a positive or negative list based on where 2009 was present: ```LM_positive = df[df['Positive'] == 2009]['Word'].tolist()
LM_negative = df[df['Negative'] == 2009]['Word'].tolist() ```

If there were 0's for both postiive and negative columns, I skipped the word. I validated this assumption by going into the resulting dataframe to check the words and the sentiment that they were associated with.

I also converted all the words into lowercase font and separated them with: ```LM_positive = [word.lower() for word in LM_positive]``` 

Then, I found the number of words in each list by using : ```num_words_neg_regx_BHR = len(neg_regx_BHR.split('|'))```
- Number of words in neg_regx_BHR: 94
- Number of words in pos_regx_BHR: 75
- Number of words in pos_regx_LM: 345
- Number of words in neg_regx_LM: 2305

# Topics: Risk, Human Capital, and Legal Proceedings
```
risk_sentiment = [
    "market",
    "financial",
    "regulatory",
    "compliance",
    "operational",
    "strategic",
    "reputation",
    "exposure",
    "vulnerability",
    "downturn"]

human_capital_sentiment = [
    "diversity",
    "equity",
    "inclusion",
    "health",
    "safety",
    "compensation",
    "benefits",
    "management",
    "workforce",
    "employee",
    "workplace",
    "culture",
    "development"]

legal_proceedings_sentiment = [
    "claim",
    "lawsuit",
    "defendant",
    "patent",
    "rights",
    "infringe",
    "litigation",
    "infringement",
    "settlement",
    "agreement",
    "district",
    "contract",
    "court",
    "state",
    "U.S.",
    "license",
    "allegation",
    "damages"]
```

# Reasoning for Topic Choices: 

### Risk: 
Risk is a broad and all-encompassing section in every firm's 10k. It is interesting to see how various different aspects of risk like market, operational, financial, legal, strategic, and industry risks play a part in a company's risk management structure. 

### Human Capital:
Human Capital also peaked my interest, as discussions revolving around diversity, equity, and inclusion have been trending in recent years. I was also interested in seeinng how firms positively, or negatively, speak on subjects like workplace equity, employee benefits, and workplace culture. 

### Legal Proceedings:
Legal Proceedings was a choice, simply because I was interested to see how much negative sentiment would be associated with legal proceedings against the company. Key words like infringement, litigation, rights, lawsuit, and claim were all common words used in 10k's. 

# Results:


# Difference between sign and magnitude: 

### Sign: 
In this case, if there is a strong positive correlation between sentiment and returns, we can expect that there would be positive returns associated with positive sentiment and negative returns associated with negative sentiment. But, if there is negative correlation, we should expect the opposite trend. If there is no significant correlation, there could be a mix of positive and negative returns across the various levels of sentiment. 

### Magnitude:
This refers to the correlation between the returns and sentiment and how strongly sentiment can influence returns. If the correlation turns out to be weak, the result might be that returns are scattered randomly around 0.0 for all levels of sentiment. In the other case, a strong correlation would output as a trend where returns consistently increase or decrease with sentiment. 


```python

```
