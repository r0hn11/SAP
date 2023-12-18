
https://github.com/SAP-samples/datasphere-fedml/blob/main/README.md
## 1. Datasphere to BigQuery
- Navigate to BigQuery -> Create a new dataset -> Add data in the dataset
- In GCP console, navigate to IAM / Service Account / Create Service Account. (SAP Datasphere & Google BigQuery connection)
```
"account_details": {
	"service account name":"<name>",
	"service account ID":"<name>@<generated_syntax>"
}
// grant this service account with project access
"role":"BigQueryJobUser
// create service acccount
```
- Navigate to the BigQuery data set and click on Sharing . Click on Add Principal. Copy paste the service account email id and Select the Assign the role "BigQuery Data Viewer" and click "Save" . 
- To create a private key for this user, click on the service account and open Keys tab. Now click Add Key -> Create new key and download it as JSON.
	- This downloaded JSON file will be used for connection between **SAP Datasphere** and **Google BigQuery**

## 2. BigQuery-Datasphere connection
### Authorizing Google connection in SAP Datasphere
- To authorize **Google BigQuery** inside **SAP Datasphere**, add the certificate which can be found in the websites certificate section.
- Navigate to www.google.com in web browser and follow these steps (Note: process may vary for different browsers)
![[dspcon1.png]]
![[dspcon2.png]]
![[dspcon3.png]]
![[dspcon4.png]]

- Export and download the certificate into local storage.
- In SAP Datasphere, navigate to ***System/Configuration*** and under **Security tab** add a new SSL/TSL certificate. Upload recently downloaded certificate (.pem or .cert)
- Connection is established

### Creating connection in SAP Datasphere
- Navigate to **Connection** tab inside Datasphere tenant to create connection in required space
- Search for **Google BigQuery** 
- Enter details as following
	- Project name (mandatory): Project name as per created in **Google cloud console**
	- Location (optional): Location for dataset
	- key: Upload the JSON key downloaded from **Google BigQuery** as mentioned in above section
	- Provide Business name & Technical name as per requirements
- Create the connection

## Azure
### Setting up space and environment
- Navigate to Azure portal. Create a new resource for Azure machine learning. Provide required details like **resource group, key vault** etc.
- Open the machine learning studio by clicking URL or 'launch studio' button and create a new folder in the notebooks section
- (Upload Retail-predictions-notebook folder as it is inside the user folder)

### Bringing Data from Datasphere into python
#### Setup
- FedML library for each platform can be installed using pip command. This documentation focuses on Azure cloud platform.
- Execute command `pip install fedml-azure --force-reinstall` to install fedML library for azure
- import the **DbConnection** class `from fedml_azure import DbConnection`
	- follow **[DbConnection class](https://github.com/SAP-samples/datasphere-fedml/blob/main/dbconnection.md)** for detailed explanation
- Execute methods of this class as per requirements.
#### Usage
- DbConnection class allows us to establish a connection between SAP Datasphere's database user and python notebook. (This class differs for each platform hence refer to relative documentation).
- Whenever the object of this class is created, a new resource is created in datasphere under **sources** section in data builder artifacts.
![[dspuser.png]]
- Once the model is ready and trained, we can use it and store the output dataframe into a new table
- Create a new table by following syntax
```
db = DbConnection()
db.create_table("CREATE TABLE FR_RL_Retail_Predictions (ID INTEGER PRIMARY KEY, retailer INTEGER, productsku INTEGER, calendar_year INTEGER, calendar_month INTEGER, Predictions FLOAT(2))")
```
- Push the newly created output data frame into this table
`db.insert_into_table('FR_RL_RETAIL_PREDICTIONS',result_df)`


## Datasphere & SAC
- Use the output table to create views and consume them in SAP Analytics cloud for required purpose