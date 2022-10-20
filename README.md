# Crowdfunding-ETL

A dataframe was created using the following .csvs:
- Campaign
- Category
- Contacts
- Subcategory
- Backers

## Deliverable 1

The first data frame consists of extracting the 'backer_id', 'cf_id', 'name', and 'email' which provides the backers information. The code used for this was:

```
backers_df = pd.DataFrame(dict_values, columns=['backer_id', 'cf_id', 'name', 'email'])
backers_df.head(10)
```

The respective output can be found below:

![Dataframe1](https://user-images.githubusercontent.com/111096246/196833893-1b61b05a-55e7-46ec-9ca2-af8c8b0a3df5.PNG)

## Deliverable 2

The previous dataframe was used, yet this time the 'name' column was split into two columns named 'first_name' and 'last_name'. The original 'name' column was dropped to further clean the dataframe.

The following code was used to split the name column into two columns named 'first_name' and 'last_name':

```
backers_df[["first_name","last_name"]] = backers_df["name"].str.split(' ', n=1, expand=True)
backers_df.head(10)
```

Afterwards, the 'name' column was dropped, and the dataframe was rearranged using the following code:

```
#  Drop the name column
backers_cleaned = backers_df.drop(['name'], axis=1)

# Reorder the columns
backers_cleaned = backers_cleaned.reindex(columns=['backer_id', 'cf_id', 'first_name', 'last_name', 'email'])
backers_cleaned.head(10)
```

The final dataframe looks like this:

![Dataframe2](https://user-images.githubusercontent.com/111096246/196836504-06208720-5c30-471b-bb47-1f80d98ef342.PNG)

## Deliverable 3

An Entity Relationship Diagram (ERD) was created to better visualize the database relationships. This was done by assigning both primary and foreign keys. A primary key is a column within a database that only has unique row values, so that whenever any row is called, there are no duplicates. A foreign key is a column within a database that is the same in another database. The final ERD can be found below:

![crowdfunding_db_relationships](https://user-images.githubusercontent.com/111096246/196837152-66bc0d58-2953-40a3-a5e7-44c6545dba54.png)

In the figure above, in the 'campaign' table for example, the cf_id has a key icon next to the subtitle. This key denotes the primary key of a table. The line connecting it to the 'backers' table visualizes the relationship that column has with the cf_id in the 'backers' table. In this example, the 'cf_id' in the 'backers' table is the foreign key.

The schema from the ERD was exported in PostrgresSQL and imported into pgAdmin4 to further edit the data. A snippet of the exported schema can be found below:

![image](https://user-images.githubusercontent.com/111096246/196837540-e60cad2f-0d19-400d-80db-530abc3e53d2.png)

Where lines 51 through to 60 denote the creation of the 'backers' table, and the ALTER TABLE snippet denotes the foreign key relationship, from a different table, but the same concept can be applied to the 'backers' table.

Lastly, the backers.csv was imported into the newly created table, and the final result can be found below:

![Table1](https://user-images.githubusercontent.com/111096246/196837811-07eb7ca6-5c6f-42fd-b888-a6bd92fe0e8e.PNG)

## Deliverable 4

Further queries were run, and in this case the query is asking to retrieve all the number of backer_counts in descending order for each `cf_id` for the "live" campaigns.

This was done by typing in the following query:

```
SELECT COUNT (ba.backer_id), ca.cf_id
FROM backers as ba
JOIN campaign as ca
ON (ba.cf_id = ca.cf_id)
WHERE outcome = 'live'
GROUP BY ca.cf_id
ORDER BY COUNT (ba.backer_id) DESC;
```

Secondly, to confirm the results from the previous query, the the "backers" table can be used to do so by typing in the following query:

```
SELECT COUNT (ba.backer_id), ba.cf_id
FROM backers as ba
GROUP BY ba.cf_id
ORDER BY COUNT (ba.backer_id) DESC;
```

Thirdly, a query is needed to create a table that has the first and last name, email address of each contact, and the amount left to reach the goal for all "live" projects in descending order. 

```
SELECT co.first_name, 
	co.last_name, 
	co.email, 
	(ca.goal - ca.pledged) AS Remaining_Goal_Amount
INTO email_contacts_remaining_goal_amount
FROM contacts as co
JOIN campaign as ca
ON (co.contact_id = ca.contact_id)
WHERE outcome = 'live'
ORDER BY Remaining_Goal_Amount DESC;
```

Lastly, using the 'email_contacts_remaining_goal_amount' table created in the previous query, a new table needs to be created that contains the email address of each backer in descending order, the first and last name of each backer, the cf_id, company name, description, end date of the campaign, and the remaining amount of the campaign goal as "Left of Goal".

```
SELECT ba.email, 
	ba.first_name, 
	ba.last_name, 
	ba.cf_id, 
	ca.company_name, 
	ca.description, 
	ca.end_date, 
	(ca.goal - ca.pledged) AS left_of_goal
INTO email_backers_remaining_goal_amount
FROM backers AS ba
JOIN campaign AS ca
ON (ba.cf_id = ca.cf_id)
ORDER BY ba.last_name, ba.email ASC;
```
