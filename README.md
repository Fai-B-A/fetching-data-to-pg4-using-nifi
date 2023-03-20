# fetching-data-to-pg4-using-nifi
## fetching data from a data lake using nifi then insert it to a pg4 database
# Prequsits
apache nifi (the version used here is 1.18.0)

postgresql (the version used here is 42.5.0)

a linux system (not mandatory)

 **everything typed in this page is applying what is learned from "Data Engineering with Python" by Paul Crickard**

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
run your code on the terminal and check if the data generated is in your chosen directory

![Screenshot of datac](https://user-images.githubusercontent.com/85634276/226403411-b2918df1-06e6-4778-9f91-775dd31776f9.png)


launch your pg4admin and make a new database, then create a an empty table to hold the generated data

![Screenshot pg4Before](https://user-images.githubusercontent.com/85634276/226402905-907143c3-c132-4d07-8ee2-67206d19b763.png)

after we set everything up, we will open nifi to set up a data pipeline for fetching data in the specified folder and insert it to postgres's created table
