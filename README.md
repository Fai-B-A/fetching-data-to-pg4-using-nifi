# fetching-data-to-pg4-using-nifi
fetching data from a data lake using nifi then insert it to a pg4 database

everything typed in this page is applying what is learned from "Data Engineering with Python" by Paul Crickard

First, we need to generate our fake data using the "faker" library in python, the code is:

from faker import Faker
import os
import json

fake=Faker()
#the destination of the generated data(should be within the nifi directory so nifi can fetch the data)
os.chdir("yourHomeDirectory/username/nifi/yourDataFolder")

id = 1
for i in range(1001):
 	name=fake.name()
 	fname=open(name+"-"+str(id)+".json","w")
 	data_element={"name":name, "age":fake.random_int(min=18, max=90, step=1),"city":fake.city(), "address":fake.street_address()}
 	data_json=json.dumps(data_element)
 	fname.write(data_json)
 	fname.close()
 	id+=1
 	
run your code on the terminal and check if the data generated is in your chosen directory
[]

launch your pg4admin and make an empty database to hold the generated data

after we set everything up, we will open nifi to set up a data pipeline for fetching data in the specified folder and insert it in the pg4s database
