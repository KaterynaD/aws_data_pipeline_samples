<H1>AWS Data Pipeline Samples</H1>

<p><b><i>MsSqlRdsToS3Template</b></i> is a template to connect to AWS RDS MS SQL and export data to a file in an S3 bucket.
To run this template you need to upload sqljdbc4.jar driver to an S3 bucket.
The driver can be found here: https://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=11774

<p><b><i>DynamoDBTableToRedshiftTemplate</b></i> loads data from a DynamoDB table to a Redshift table.

<p><b><i>MutilpleDependencies</b></i> is an example when Action1 and Action2 can be run in parallel, but Action3 should wait for the completion both actions.

<p><b><i>SupportDW_Create</b></i> is an example of a Support Data Warehouse creation from historical files saved in an S3 bucket.
The process loads D_Calendar, D_Priorities, Products, Analysts, Cases and Logs tables in parallel. At the next stage, 
D_Products dimensional table is created and the  products hierarchy is flatten in the load SQL script,
D_Analysts dimensional table is created and analysts historical data are loaded in the slowly changing dimension type 2.
F_Cases (fact table) is created and loaded at the last stage, when dimensional data are ready (surrogate keys are used) and logs 
are available to calculate times a case spent in each status.  

<p><b><i>SupportDW_Update</b></i> is a similar to  SupportDW_Create but at the first stage the data are loaded from an application tables
in MS SQL and logs data are loaded from a DynamoDB table.
<img src="https://github.com/KaterynaD/aws_data_pipeline_samples/blob/master/temp_process.png"></img>
<H1>Installation</H1>
<ol>
<li>If you never used AWS Data Pipeline before you need to create AWS IAM roles to run the samples using AWS CLI.

<br><h3><i>$> aws datapipeline create-default-roles </i></h3>
 
<li>Create the pipelineId by calling the aws data pipeline create-pipeline command.
 
<br><h3><i>$> aws datapipeline create-pipeline --name MsSqlRdsToS3Template --unique-id MsSqlRdsToS3Template </i></h3>

<p>   
  You will receive a pipelineId like this.
<br>

<br>{
<br>    "pipelineId": "df-078827623PVY9KS3XNLM"
<br>}


<li>Download the MsSqlRdsToS3Template.json sample pipeline definition and adjust parameters values in the file to your environment.
Or you can provide your parameter values in aws data pipeline put-pipeline-definition command.

<li>Upload and validate your pipeline definition by calling the aws data pipeline  put-pipeline-definition command.
  
  
  <br><h3><i>$> aws datapipeline put-pipeline-definition --pipeline-id df-078827623PVY9KS3XNLM --pipeline-definition file://MsSqlRdsToS3Template.json </i></h3>
  
<p>If your pipeline definition is valid you will receive a message like this. Otherwise, correct the file and repeat the command.
<br>{
<br>    "validationErrors": [],
<br>    "errored": false,
<br>    "validationWarnings": []
<br>}

  
<li>Activate the pipeline by calling the aws datapipeline activate-pipeline command. 
This will cause the pipeline to start running.

  <br><h3><i>$> $> aws datapipeline activate-pipeline --pipeline-id df-078827623PVY9KS3XNLM</i></h3>
  
<p>There is no output in this command
   
<li>Check the status of your pipeline

  <br><h3><i>>$ aws datapipeline list-runs --pipeline-id df-078827623PVY9KS3XNLM</i></h3>
