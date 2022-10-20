# Crowdfunding-ETL

A dataframe was created using the following .csvs:
- Campaign
- Category
- Contacts
- Subcategory
- Backers

## Deliverable 1

The first data frame consists of extracting the 'backer_id', 'cf_id', 'name', and 'email' which provides the backers information. The code used for this was:

'''
backers_df = pd.DataFrame(dict_values, columns=['backer_id', 'cf_id', 'name', 'email'])
backers_df.head(10)
'''

as seen below:

![Dataframe1](https://user-images.githubusercontent.com/111096246/196833893-1b61b05a-55e7-46ec-9ca2-af8c8b0a3df5.PNG)

## Deliverable 2

