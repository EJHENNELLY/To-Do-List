# TaskTracker for To-Do List
Build an End-to-End to-do list in AWS with QuickSight, DynamoDB, Athena, S3, IAM, and Glue 

In this project we are going to use QuickSight to visualize the tasks on our to-do list with AWS using the following services:

1. QuickSight - Analytics Visualization Tool
2. DynamoDB - Storage for our dataset
3. Glue - Transforms data so that you can create Lambda Function
4. Lambda - Connects DynamoDB dataset to Athena
5. Athena - Used to query the data
6. S3 - Spill storage
7. IAM - Gives proper permissions

What is needed prior to starting this project -
- A configured AWS account with access to the console
- A QuickSight account (you can get a free trial for 30 days on AWS)

Project Overview:

Step 1 = Create a place to store our tasks
Step 2 = Connect the database to Athena so we can query it
Step 3 = Give Quicksight the permission to use our data
Step 4 = Create Visualizations with QuickSight
<br/>
***
STEP 1: CREATE A PLACE TO STORE OUR TASKS
***
<br/>
Open up the AWS console -> Navigate to DynamoDB -> Select "Create table" <br/>
Choose a table name, Mine is "MyTasksTable" <br/>
Create a partition key to retrieve table items, Mine is "TaskID". You can leave it as a string. <br/>
Create a sort key to search among items that have the same partition key, Mine is "Category" <br/>
Click Create Table
![Screenshot (40)](https://github.com/user-attachments/assets/0e324c37-7e58-4c02-8625-e02cb97fed55)
Click into your table once it is created -> select "explore items" (Note the table is empty, We need to add items) <br/>
Let's add some items! <br/>
Click create item -> Select "JSON view" -> Create at least 10 random tasks using the following format: <br/>
![Screenshot (41)](https://github.com/user-attachments/assets/4f018c2a-cc46-4c10-97e2-44173ae8c83a)
<br/>
(You can use chatgpt to make them quickly if you want) <br/>
Paste them into the table one at a time. <br/>
And there you have it, A table with our tasks stored in DynamoDB!

<br/>
***
STEP 2: CONNECT THE DATABASE TO ATHENA
***
<br/>

In this step, we need to connect dynamodb to athena. There is one issue - Athena does not query dynamodb because it is a noSQL database. So, we will have to connect the 2 services with Lambda. To do this, we will have to use Glue to transform our data. <br/>
  
Right click the AWS logo in the top left corner and select "open in new tab" <br/>
Navigate to Athena -> Using the side bar navigate to "Data sources and catalogs" -> click "create data source" <br/>

![Screenshot (42)](https://github.com/user-attachments/assets/c5360297-8d38-4e71-ab6c-0e85deb1b5a3)

Click the DynamoDB option, scroll down and click next<br/>
Name the data source, I used "Dynamodbtasks" -> create an AWS Glue name if you want, I left mine as default <br/>
We need an S3 bucket for our spill location. Navigate to S3. Click create bucket. Give it a name, Mine is athena-spill-bucket-ert <br/>
Leave everything else as default and create the bucket.

![Screenshot (43)](https://github.com/user-attachments/assets/8b29320d-478c-4258-ad8f-b2ba45ecd882)

Now back to Athena. For spill location click "browse s3" and select the bucket you made. -> leave everything else as is and create datasource. <br/>
It may take a minute. Once that is done use the side bar to click "query editor" -> select your data source -> Go to settings and add your s3 bucket. <br/>

  ![Screenshot (44)](https://github.com/user-attachments/assets/75473ac7-1837-4ce9-8750-098373e71047)

Go back to query editor and create a simple query: 
![Screenshot (45)](https://github.com/user-attachments/assets/b6761992-6ba2-4bd4-b0c5-1274ad812f12)

Run the query to make sure it works. <br/>
  ![Screenshot (46)](https://github.com/user-attachments/assets/eb53e005-27ff-4003-a84d-0614278890ca)

Great it works!

<br/>
*** 
STEP 3: GIVE QUICKSIGHT PERMISSION TO USE YOUR DATA
***
<br/>

Right click the AWS logo in the top left corner and select "open in new tab" <br/>
Navigate to IAM -> select "roles" -> search for the quicksight role. -> click add permissions and select "inline policy" <br/>
![Screenshot (47)](https://github.com/user-attachments/assets/0b46383e-d0da-45f8-9893-73d6164d7b56)

Type in the below policy. Navigate to Lambda to get your arn and region and paste that into the "resource section of the policy"
![Screenshot (49)](https://github.com/user-attachments/assets/c07c0648-1643-4c72-a9ea-13a14271b8a1)

Click Next, Give it a name, Mine is quicksight-invoke-lambda <br/>
![Screenshot (50)](https://github.com/user-attachments/assets/8ec05a82-5412-4e5c-9aea-8cb183dba014)

Do the same for the s3. Replace the "bucket name" with your bucket, Name it and create policy <br/>
![Screenshot (52)](https://github.com/user-attachments/assets/a16dc39a-a560-4204-abd2-6865e002eead)

![Screenshot (53)](https://github.com/user-attachments/assets/19b3ed89-fa61-4be5-9fba-fb79784aca7b)

All set with Permissions!<br/>

<br/>
***
STEP 4: CREATE YOUR DATA VISUALIZATIONS
***
<br/>

Navigate to QuickSight -> datasets -> new dataset -> connect Athena
![Screenshot (54)](https://github.com/user-attachments/assets/20234765-d825-41aa-8d51-b209c3d07e30)

![Screenshot (56)](https://github.com/user-attachments/assets/feb97d45-d1e8-46cc-be35-eceb9c3cd6aa)

![Screenshot (57)](https://github.com/user-attachments/assets/c526a4fe-a650-4683-a434-25fa0c09bdee)

Voila, You have your visual data! <br/>
![Screenshot (58)](https://github.com/user-attachments/assets/59248914-c2a5-42e9-a288-ca5f3b09fd40)
Play around and create some visuals for your data!
![Screenshot (59)](https://github.com/user-attachments/assets/85137e30-d0cd-4c0b-a0a7-6e9772ecbb4b)

But that is not the end. Let's make sure that if we add another item to our table, it is reflected.
Switch to DynamoDB. Create a new item. <br/>
![Screenshot (60)](https://github.com/user-attachments/assets/16f5a90f-3c6b-404d-a33c-9219e775bf0f)

Go back to QuickSight ->go to the home page -> go back to your dataset -> It should show a new task.<br/>
![Screenshot (61)](https://github.com/user-attachments/assets/10cc0515-5088-4ede-99ea-4dff4b15eef8)

Great Work! <br/>
Make Sure to close out and delete all resources so you do not get charged!!!



  
  
  
