# fetching-data-to-pg4-using-nifi

# Prerequisites
Apache nifi (the version used here is 1.18.0)

Postgresql (the version used here is 42.5.0)

A linux system (not mandatory)

------
 **Everything typed in this page is applying what is learned from "Data Engineering with Python" by Paul Crickard**
------

First, we need to generate our fake data using the "faker" library in python, the code is:
```
from faker import Faker
import os
import json

fake=Faker()
#the destination of the generated data(should be n a folder within the nifi directory so nifi can fetch the data)
os.chdir("yourHomeDirectory/username/nifi/yourDataFolder")

id = 1
for i in range(1001):
 	name=fake.name()
 	fname=open(name+"-"+str(id)+".json","w")
 	data_element={"id":id, "name":name, "age":fake.random_int(min=18, max=90, step=1),"city":fake.city(), "address":fake.street_address()}
 	data_json=json.dumps(data_element)
 	fname.write(data_json)
 	fname.close()
 	id+=1
```
Run your code on the terminal and check if the data generated is in your chosen directory

![Screenshot of datac](https://user-images.githubusercontent.com/85634276/226403411-b2918df1-06e6-4778-9f91-775dd31776f9.png)


Launch your pg4admin and make a new database, then create a an empty table to hold the generated data

![Screenshot pg4Before](https://user-images.githubusercontent.com/85634276/226402905-907143c3-c132-4d07-8ee2-67206d19b763.png)
![Screenshot pg4Before](https://user-images.githubusercontent.com/85634276/226704981-68e184ce-1207-4374-a45c-f3a02402a0fa.png)

After we set everything up, we will open nifi to set up a data pipeline for fetching data in the specified folder and insert it to postgres's created table

going to nifis GUI, drag the "GetFile" processor to your canvas then configure it whats shown in the image below

![getfileProcessor](https://user-images.githubusercontent.com/85634276/226692631-62511183-639a-4046-a9b8-a6c0cbf526c3.png)

the GetFile processor will fetch the data found in the input "directory" field. In the input directory field, put the path of the folder holding the generated data.
for the "file filter" field, I left it with its default value since I want all the data stored in that directoy to be fetchet but if you want to only fetch data with a certain pattern on their name, then you can enter the specified pattern there.


drag the "EvaluateJsonPath" processor to the canvas, change the "Distination" field to "flowfile-attribute", then add new properties that correspondes to the columns we specified in pg4 and the data elements in our faker python program.

![evaluatejsonpathProcessor](https://user-images.githubusercontent.com/85634276/226697987-f78c7034-b9be-48c0-9dea-6d12672432f0.png)


drag the "PutSQL" processor to the canvas then click on the "create a new service" option on the "JDBC Connection pool" and choose "DBCPConnectionPool"
![dbcCreate](https://user-images.githubusercontent.com/85634276/226701844-bb1db0f0-0148-4eb1-bdce-77183f42651d.png)

then configure the DBCP as shown below

![dbcDetails](https://user-images.githubusercontent.com/85634276/226702055-3850f4eb-cd87-48f4-a039-8ee4fd0a12de.png)

on the first field, you need to enter your pg4admins link with the database which has the table that you want to access
you need to enter the path of which the postgres is stored on the third field (postgres should be stored whithin nifi)

After we configured the JDBC, we change the following fields under the "PutQul" processor as shown below:

![putsql precessor](https://user-images.githubusercontent.com/85634276/226703636-e270e300-4e31-44a6-8790-818588436aeb.png)


we connect the processors together and check if the files got inserted in our table in pg4Admin

![screenshot minidatalake process](https://user-images.githubusercontent.com/85634276/226704535-c11e8cca-7b10-4107-a6f3-bcba39a782f8.png)
![pg4DB](https://user-images.githubusercontent.com/85634276/226704750-485d147c-aaae-4e38-a2d8-be0e73828794.png)






