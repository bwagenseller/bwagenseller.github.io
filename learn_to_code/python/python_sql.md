# Learning To Code: 

# Before We Begin

# HP_VerticaTest.py

```

import pandas as pd

import hp_vertica_client

def connectAndTest():
	db = hp_vertica_client.connect(host='verticapride.it.att.com', database="PDW", port="5433", user='USER', password='PASSWORD')
	cur = db.cursor()

	"""
	can ALSO be done a la
	#As a connection string:
	conn = hp_vertica_client.connect("database=verticadb user=user_name password=pwd")

	#As a set of keyword arguments:
	conn = hp_vertica_client.connect(database="verticadb", user='user_name', password='pwd')

	#As a dictionary:
	dict_conn = {'database': 'verticadb', 'user': 'user_name', 'password': 'pwd'}
	conn = hp_vertica_client.connect(dict_conn)
	"""

	cur.execute("SELECT MME, Pool FROM PDW_ALUMME_VIEWS.MME_MAF_HSS WHERE Datetime >= '2017-04-18' LIMIT 5")
	if cur.rowcount == 0:
		print('No rows to return!')
	else:
		rows = cur.fetchall()
		for i, row in enumerate(rows):
			print row


if __name__ == "__main__":
	print "Printing Results..."
	connectAndTest()

```

# mysqlPython.py

```

#!/usr/bin/anaconda/python2.7/bin/python
import pandas as pd
import numpy as np
import mysql.connector as msql
import os
import sys
import argparse
import RosettaDateFunctions as RosDate
import RosettaDatabaseConnections as RDBC

def testFunc():
	parser = argparse.ArgumentParser(description='Process some integers.')
	parser.add_argument("-u", "--user", default="Max Power", help="Username")
	parser.add_argument("-p", "--printProgressToScreen", type=int, default="0", choices=[0,1], help="Print the progress to the screen? (0 or 1)")
	parser.add_argument("-i", "--myInt", type=int, default="{:02d}".format(datetime.date.today().month), choices=[1,2,3,4,5,6,7,8,9,10,11,12], help="Numeric value of the month")
	#parser.add_argument('integers', metavar='N', type=int, nargs='+',
	#	help='an integer for the accumulator')
	#parser.add_argument('--sum', dest='accumulate', action='store_const',
	#	const=sum, default=0,
	#	help='sum the integers (default: find the max)')
	try:
		args = parser.parse_args()
	except:
		parser.error("Invalid Month.")
		sys.exit(0) 
	
	printProgressToScreen = args.printProgressToScreen
	print "The user is {} and the month is {} and things should be printed to the screen is {}".format(args.user, args.myInt, printProgressToScreen)
	#print "The user is {} and the month is {}".format(args.user, args.myInt)

def testDatabase():
	"""
	A) The rollback only effects things that are not comitted - so if a rollback happens after something is committed its fine
	B) For MySQL, ALL parameters are represented with a %s; %d and %f does not exist!
	"""
	
	#my error script uses this dictionary to email the Rosetta admins the details of a failed script, should it fail
	failureDictionary = {}
	failureDictionary['foreignScriptID'] = 999
	failureDictionary['additionalNotes'] = 'My testing'
	failureDictionary['errorString'] = 'TESTING!!!'
	failureDictionary['dataDateTime'] = RosDate.addSecondsToNow()

	#create the connection
	cnx, connSuccessful = RDBC.getRosettaConnection(hostName = "rdm-rosetta21.wnsnet.attws.com")

	if connSuccessful == 0:
		print "Connection to the database could not be established - exiting immediately"
		sys.exit(0) 

	###an example of how to get simple information from a query pull
	SQL = "SELECT 'aa' AA, 'bb' BB FROM DUAL"
	myCursor = cnx.cursor()
	myCursor.execute(SQL)
	for (AA, BB) in myCursor: 
		if (AA == None): AA = 'aa'
		if (BB == None): BB = 'bb'			
	myCursor.close()	


	#example of how to run a simple query where you just want it to run
	#note this is good for everything that is not "INSERT INTO.....VALUES" - see that example below
	SQL_Statement = "CREATE TABLE IF NOT EXISTS Administration.AAA (myID INT NOT NULL AUTO_INCREMENT, OneT INT, Two VARCHAR(15), PRIMARY KEY (myID))"
	print RDBC.AttemptChangeQuery(cnx, SQL_Statement, failureDictionary = failureDictionary, printProgressToScreen = 1)

	#This is how you insert into a table via values - note the construction of the list
	#also note that each list contains a tuple, which are immutable - so to build an inner tuple on the fly you have to build a list, and then 
		#add the temp list onto the end of SQL_Data via append()
	SQL_Statement = "INSERT INTO Administration.AAA (OneT, Two) VALUES (%s, %s)"
	SQL_Data = []
	SQL_Data.append((0, 'Zero'))
	SQL_Data.append((1, 'One'))
	SQL_Data.append((2, 'Two'))
	print RDBC.AttemptChangeQuery(cnx, SQL_Statement, SQL_Data, failureDictionary, printProgressToScreen = 1)

	#pulling data from a table
	#note this example splits the query and the data; this is not required (although preferred), and if its not used simply eliminate the 'data' parameter
		#if you do parameterize the variables, note that 'data' is a tuple, and that %s is used for EVERYTHING - strings and numbers alike
		#also note that the single quotes around strings are not needed when parameterizing
	cursor = cnx.cursor()
	SQL = ("SELECT OneT, Two FROM Administration.AAA WHERE OneT > %s AND Two <> %s")
	data = (-1, 'one')
	cursor.execute(SQL, data)
	for (OneT, Two) in cursor:
		print "{} is spelled {}".format(OneT, Two)
	cursor.close()


	###Begin 'New School' method
	#this method is the new way of dealing with the data.  It uses what is known as a 'pandas dataframe'.  Dataframes are important objects that hold data; they are used often in Big Data so
	#it would be helpful to learn them well; we will call our dataframe df here, so every time you see df we are deaking with a pandas dataframe variable
	#using the new method, there is no need for cursors or for loops; this means the code is cleaner AND its faster as well!

	#Pandas implementation

	#This is the way you set the SQL statement using the Pandas method; note that you still use variables but instead of using %s you will use %(variableName)s, where variableName is 
	#the name of the varable
	#CRITICAL: Note the order AND NAME of each column returned; you will be using this name time and again in the code!
	SQL = ("SELECT DataDate, eSMLC, COUNT(*) CountOfSectors FROM Inventory.eSMLC_4G_CellData WHERE DataDate > %(myMinDate)s GROUP BY DataDate, eSMLC")

	#you now must set the variable, which is also a bit different.  This uses another dictionary (much like we saw above)
	#the order must match that of the %(variableName)s in the SQL statement
	myParams={"myMinDate":'2016-02-19'}

	#finally save the data to a dataframe (the df variable); the variables are striaghtforward with the exception of the index_col.  The index_col is the column that will be used to index
	#the data; its important to set this correctly as its used for matching data from other dataframes as well as labels in graphs
	#since we will eventually plot the bar graph of sector count by eSMLC, we will set this to the eSMLC column
	df = pd.read_sql(sql=SQL, params=myParams, con=cnx, index_col='eSMLC')

	#WOOOHOOOOOO all rows returned in SQL are now in the variable df!

	###End 'New School' method

	cnx.close()

if __name__ == "__main__":
	testDatabase()


```

# Python_RosettaTraining.py

```

#!/usr/bin/anaconda/python2.7/bin/python

"""
Comments that span multiple lines are embedded between two triple double quotes (if thats cofusing, just look at the line above this to see what I am talking about)

Other one line comments start with a # like this:
#This is a comment
#This is another comment

The only exception to this is the shebang line, which is the VERY first line in the script.
The shebang line allows us to simply execute the script with its name only (and not have us type 'Python' before launching the script)

To run this: ./Python_RosettaTraining.py
"""

#import python libraries
#note that you must specify the import when referencing something from that library (for example, environ is from 'os' and must be referenced as os.environ)
#note its also possible to give these libraries a temp shorter name (see renaming pandas as 'pd' below)
import sys
import os
import mysql.connector
import pandas as pd 

#this imports the functionality to plot graphs; note that the next three lines MUST be present and MUST be in this order!
import matplotlib
matplotlib.use('Agg') # Must be before importing matplotlib.pyplot or pylab!
import matplotlib.pyplot as plt


#this is how a function is created in Python.  Note that
#	you can set the variables to defaults in the definition of the function.
#	if you put the variables in order when calling the function you do not have to use the parameter names; that said if you want to send the parameters out of order 
#		you MUST specify the parameter when you call the function
#	you do not need to wrap all the code in the function in anything like brackets, parenthesis, etc; that said it depends on the whitespace before the line of code (much like the rest of Python)
def getRosettaConnection(hostName = 'NULL', userName = 'NULL', userPass = 'NULL', userDatabase = None):

	connSuccessful = 0
	if(hostName == "NULL"):
		#if the hostname was not set, use the environment variable set in our .env file
		#if you don't know about this variable its probably not set for you, so you will have to set the environment variable
		hostName = os.environ['ROSETTA_PRIMARY_SERVER']

	if(userName == 'NULL'):
		userName = os.environ['ROSETTA_USER']

	if(userPass == 'NULL'):
		userPass = os.environ['ROSETTA_PASS']

	#this is the config dictionary
	#A dictionary works much like an array, but instead of a number to reference the row it uses a word
	#for example to use the 'host' entry in the dictionary you could reference it as such in the code: config['host']
	#note the setup of the dictionary, which is 'entryInDictionary': variableThatHoldsTheValue
	#The config dictionary is needed to set up the MySQL connection and this format is required
	#that said you can use dictionaries for all kinds of things
	config = {
		'user': userName,
		'password': userPass,
		'host': hostName,
		'database': userDatabase,
		'raise_on_warnings': False
	}

	#this is an example of a print statement that prints to the screen; note that {} denotes a variable, and then the variables in the format() section match up to each {}
	#if you do not have one variable for every {} you will get an error
	print "Attempting to connect to database {}".format(config['host'])

	#the MySQL connection needs a 'connection' variable that needs to successfully connect; here we initialize it, but we will actually set it too
	cnx = None

	#now we need to actually set the connection variable.  For now dont worry about what is going on here exactly, but know that Python needs to 
	#set this variable in what is known as a try/catch block, so if there is an error (i.e. if Rosetta is down) it can deal with the error gracefully
	try:
		cnx = mysql.connector.connect(**config)
	except mysql.connector.Error as err:
		if err.errno == mysql.connector.errorcode.ER_ACCESS_DENIED_ERROR:
			print("Something is wrong with your user name or password")
		elif err.errno == mysql.connector.errorcode.ER_BAD_DB_ERROR:
			print("Database does not exist")
		else:
			print(err)
	else:
		connSuccessful = 1

	#cnx now holds the connection info!

	#this last line returns specified varaibles used to the code that calls this function
	#the connection AS WELL AS another variable tha denotes if the connection was suffessful (1) or not (0)
	return cnx, connSuccessful


#This is known as the 'main' function
#THIS IS WHAT ACTUALLY LAUNCHES YOUR CODE; PUT FUNCTIONS ABOVE THIS AND THE ACTUAL EXECUTION BELOW
#USE THIS FORMAT EXACTLY
if __name__ == "__main__":

	#call our function above, which returns our connection needed as well as if the connection was successful or not
	myConnection, connSuccess = getRosettaConnection(hostName = 'wfs-mad06-bth9.wnsnet.attws.com', userName = 'tedscheckler', userPass = 'emporium')

	#IF the connection was successul, continue
	if connSuccess == 1:

		###This is the 'Old school' implementation of getting data from the database; it will work but its longer to code and does not perform as well as the 'newer' method
		#for the old school method we need a cursor object that will save all of the returned rows; initialize it here
		myCursor = myConnection.cursor()

		#build the query we will send to Rosetta
		#specifically it gets sector counts from Rosetta for each eSMLC - but only if the data is fresher than 2/19
		#note that it uses %s to represent the variables; also note it is ALWAYS %s no matter the variable
		#finally, do NOT wrap text or dates in single quotes - that is done in another line
		#for now just note how many %s used and the order of them
		#CRITICAL: Note the order AND NAME of each column returned; you will be using this name time and again in the code!
		SQL = ("SELECT DataDate, eSMLC, COUNT(*) CountOfSectors FROM Inventory.eSMLC_4G_CellData WHERE SAS_Blade_RosettaID > %s AND DataDate > %s GROUP BY DataDate, eSMLC")

		#the variable 'data' is used to store the list of variables for the query; in our case, its 0 (so the rosetta ID must be gretaer than 0) and the minimum data date (2/19)
		data = (0, '2016-02-19')

		#execute the statement, sending the query and the variable listing
		myCursor.execute(SQL, data)
		#cycle through each row returned; make sure to include a variable name for each column selected in the SQL statement
		#also make sure to get the cursor name correct
		for (DataDate, eSMLC, CountOfSectors) in myCursor:
			#print out the row returned
			print "eSMLC {} has {} sectors and was last recorded for date {}".format(eSMLC, CountOfSectors, DataDate)

		#it is CRITICAL to clsoe the cursor once you are done with it!
		myCursor.close()
		###end of 'old school' method

		###Begin 'New School' method
		#this method is the new way of dealing with the data.  It uses what is known as a 'pandas dataframe'.  Dataframes are important objects that hold data; they are used often in Big Data so
		#it would be helpful to learn them well; we will call our dataframe df here, so every time you see df we are deaking with a pandas dataframe variable
		#using the new method, there is no need for cursors or for loops; this means the code is cleaner AND its faster as well!

		#Pandas implementation

		#This is the way you set the SQL statement using the Pandas method; note that you still use variables but instead of using %s you will use %(variableName)s, where variableName is 
		#the name of the varable
		#CRITICAL: Note the order AND NAME of each column returned; you will be using this name time and again in the code!
		SQL = ("SELECT DataDate, eSMLC, COUNT(*) CountOfSectors FROM Inventory.eSMLC_4G_CellData WHERE DataDate > %(myMinDate)s GROUP BY DataDate, eSMLC")

		#you now must set the variable, which is also a bit different.  This uses another dictionary (much like we saw above)
		#the order must match that of the %(variableName)s in the SQL statement
		myParams={"myMinDate":'2016-02-19'}

		#finally save the data to a dataframe (the df variable); the variables are striaghtforward with the exception of the index_col.  The index_col is the column that will be used to index
		#the data; its important to set this correctly as its used for matching data from other dataframes as well as labels in graphs
		#since we will eventually plot the bar graph of sector count by eSMLC, we will set this to the eSMLC column
		df = pd.read_sql(sql=SQL, params=myParams, con=myConnection, index_col='eSMLC')

		#WOOOHOOOOOO all rows returned in SQL are now in the variable df!

		###End 'New School' method

		#ITS IMPORTANT TO CLOSE THE CONNECTION - SO DO SO
		myConnection.close()

		#Want to save the data to a CSV? SIMPLE! Its ONE line of code!
		df.to_csv("eSMLC_SectorCounts.csv")

		#this displays the contents of the dataframe
		print df

		print

		#This displays a portion of the dataframe; 0:4 means 'print the first 5 rows' and the second : means 'print all columns'
		#There are many, MANY ways to slice the data like this; it would be best to research the ways this can be done
		#Its possible to spend weeks on slicing a pandas dataframe, there are many ways to do it!
		print df.ix[0:4,:]

		#this prints the data to a bar graph that will show the counts of sectors per SAS blade.
		#note that the column to be printed is specified by [['CountOfSectors']] and ax is used to save the plot info
		ax = df[['CountOfSectors']].plot(kind='bar', figsize=(25,20), title ="Sector Count by eSMLC",legend=True)
		ax.set_xlabel("eSMLC",fontsize=12)
		ax.set_ylabel("Count of Sectors",fontsize=12)

		#this actually saves the plot.  its not intuitive as to why this works, but just know that all plots you generate will be saved with this command
		plt.savefig('eSMLC_SectorCount.jpg')

```

# PythonOracleExample.py

```

#!/usr/bin/anaconda/python2.7/bin/python
import pandas as pd
import numpy as np
import mysql.connector as msql
import os
import sys
import subprocess
import re
import cx_Oracle

import smtplib
import mimetypes
from email.mime.multipart import MIMEMultipart
from email import encoders
from email.mime.audio import MIMEAudio
from email.mime.base import MIMEBase
from email.mime.image import MIMEImage
from email.mime.text import MIMEText

def sendEmailWithBasicAttachment(fileToSend, To=None, Subject = '', Body = None, IsHTML=0, Preamble = ''):
	"""
	This function sends a basic file with a possible message; if the message was sent it returns a 1, otherwise a 0
	"""
	success = 1
	
	ROSETTA_OFFICIAL_EMAIL_ADDR = os.environ['ROSETTA_OFFICIAL_EMAIL_ADDR']
	ROSETTA_SMTP_SERVER = os.environ['ROSETTA_SMTP_SERVER']	
	ROSETTA_ADMIN_EMAIL_ADDRS = os.environ['ROSETTA_ADMIN_EMAIL_ADDRS']
	
	if (To is None):
		To = ROSETTA_ADMIN_EMAIL_ADDRS
	
	msg = MIMEMultipart()
	msg['Subject'] = Subject
	msg['From'] = ROSETTA_OFFICIAL_EMAIL_ADDR
	msg['To'] = To
	msg.preamble = Preamble
	
	try:
		if ((Body != '') & (Body is not None)):
			if IsHTML==1:
				SentBody = MIMEText(Body, 'html')
			else:
				SentBody = MIMEText(Body, 'plain')		
		
			msg.attach(SentBody)	

		ctype, encoding = mimetypes.guess_type(fileToSend)
		if ctype is None or encoding is not None:
			ctype = "application/octet-stream"

		maintype, subtype = ctype.split("/", 1)

		if maintype == "text":
			fp = open(fileToSend)
			# Note: we should handle calculating the charset
			attachment = MIMEText(fp.read(), _subtype=subtype)
			fp.close()
		elif maintype == "image":
			fp = open(fileToSend, "rb")
			attachment = MIMEImage(fp.read(), _subtype=subtype)
			fp.close()
		elif maintype == "audio":
			fp = open(fileToSend, "rb")
			attachment = MIMEAudio(fp.read(), _subtype=subtype)
			fp.close()
		else:
			fp = open(fileToSend, "rb")
			attachment = MIMEBase(maintype, subtype)
			attachment.set_payload(fp.read())
			fp.close()
		encoders.encode_base64(attachment)

		attachment.add_header("Content-Disposition", "attachment", filename=fileToSend)
		msg.attach(attachment)

		s = smtplib.SMTP(ROSETTA_SMTP_SERVER)
		s.sendmail(ROSETTA_OFFICIAL_EMAIL_ADDR, msg['To'], msg.as_string())
		s.quit()
	except:
		success = 0
	
	return success

def testFunc():
	print "This is an example of several ways you can connect to an Oracle databse - both NDR and Optima. It simply prints the Oracle version and then a dataframe pulled back from the database's dual test table. Test ID: {}\n\n".format(6)

	userName = os.environ['OPTIMA_USER']
	userPass = os.environ['OPTIMA_PASS']
	
	#Optima connection
	myCon = cx_Oracle.connect(user=userName,password=userPass, dsn="(DESCRIPTION=(FAILOVER=on)(LOAD_BALANCE=on)(ADDRESS_LIST=(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p1-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p2-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p3-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p4-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p5-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p6-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p7-vip.wnsnet.attws.com)(PORT=1701))(ADDRESS=(PROTOCOL=TCP)(HOST=opm01p8-vip.wnsnet.attws.com)(PORT=1701)))(CONNECT_DATA=(SERVER=dedicated)(SERVICE_NAME=OPTIMA_REPORT_PWRUSR)))")

	#for reference, here is the NDR connection
	#myCon = cx_Oracle.connect(userPassSmash + '@p1ndr2d1.wnsnet.attws.com:1524/p1ndr2d1')
	
	print myCon.version + "\n\n"

	#get the data to a dataframe called 'df'
	df = pd.read_sql(sql="SELECT 'aaa' myA FROM DUAL", con=myCon)

	#print the csv file
	df.to_csv("aref.csv")

	#call the sendmail function with the necessary parameters
	sendEmailWithBasicAttachment("aref.csv", To='ah4372@att.com', Subject = 'Python Email Test', Body = "This is the body of the email", IsHTML=1)


	print df

	#close the connection
	myCon.close()

	#delete the file
	subprocess.check_output(['rm', "aref.csv"])			


if __name__ == "__main__":
	testFunc()

```

# Multiprocess/Threadded Example with MySQL Inserts

In this example we have a file (mySQL_CommandsFile.txt) that has hundreds of SQL commands (not queries, but things like UPDATE, INSERT, etc - and each is contained on exactly ONE line (so no spanning lines per SQL entry)) that we wish to upload to a MySQL database.

We do this using multiple processes, which in turn launch multiple threads; each thread processes one SQL command.

```

import argparse
import sys
import os
import socket
import subprocess
import datetime
import math
import random
import concurrent.futures
import threading
import time
import mysql.connector
from mysql.connector import pooling
import platform
import string
import sqlalchemy

import pandas as pd
import numpy as np

###Globals
#these globals will be available in ANY function - but they must be defined in the function as a 'global' as well. It seems the onle one that does not is the lock (currentDriveLock)
#I am not sure if globals can be used in a multiprocess environment - as variables can only be shared by threads and not between processes.
threadsCompletedInCurrentDrive = 0
failuresInCurrentDrive = 0
currentDriveLock = threading.Lock()


def runThreadedQuery(cnxPool, SQL_Statement):
	global threadsCompletedInCurrentDrive
	global failuresInCurrentDrive

	try:
		cnx = cnxPool.get_connection()
		
		#actually run the query
		cursor = cnx.cursor()
		cursor.execute(SQL_Statement)
		cnx.commit()#this commit is critical!!!
		cursor.close()
		cnx.close()
		
		#acquire the lock and update the global variable threadsCompletedInCurrentDrive; the lock is then released
		currentDriveLock.acquire()
		threadsCompletedInCurrentDrive = threadsCompletedInCurrentDrive + 1
		currentDriveLock.release()			
	except:
		#acquire the lock and update the global variable failuresInCurrentDrive; the lock is then released
		currentDriveLock.acquire()
		failuresInCurrentDrive = failuresInCurrentDrive + 1
		currentDriveLock.release()	

		#return the process ID, a -1 (indicating failure), and the SQL statement itself (so the list that collects this in the calling function knows exactly which statements failed)
		return [os.getpid(), -1, SQL_Statement]
	else:
		#return the process ID, a 1 (indicating success), and the SQL statement itself (so the list that collects this in the calling function knows exactly which statements were successful)
		return [os.getpid(), 1, SQL_Statement]


def runSQL_Threads(ConnDict, SQL_Commands, maxThreadsPerProcess):
	"""
	This is a generic function that takes a list of SQL commands and threads them
	"""
	results = []
	startTime = None
	global threadsCompletedInCurrentDrive
	global failuresInCurrentDrive	
	
	#use a pool connection instead of a normal one, as the normal one will not properly close connections and will just keep adding them until the code bombs
	#using a pool will sidestep this
	config = {
		'user': ConnDict['userName'],
		'password': ConnDict['userPass'],
		'host': ConnDict['hostName'],
		'database': None,
		'raise_on_warnings': False
	}
	cnx = None

	try:
		#specifically make a pooled connection
		#the pool name can be anything
		cnx = pooling.MySQLConnectionPool(pool_name = "mypool", pool_size = (maxThreadsPerProcess+3), **config)
			
		
	except mysql.connector.Error as err:
		if err.errno == mysql.connector.errorcode.ER_ACCESS_DENIED_ERROR:
			print "Something is wrong with your user name or password"
		elif err.errno == mysql.connector.errorcode.ER_BAD_DB_ERROR:
			print "Database does not exist"
		else:
			print err
	else:
		connSuccessful = 1	
	
	
	if (connSuccessful == 1):
		
		#actually launch the threads
		with concurrent.futures.ThreadPoolExecutor(max_workers=maxThreadsPerProcess) as executor:
			tasks = []
			
			for i in range(len(SQL_Commands)):
				#submit the threads - one for each line in SQL_Commands
				task = executor.submit(runThreadedQuery, cnx, SQL_Commands[i])
				tasks.append(task)
			
			#This will wait for all threads to finish - specifically the code will stall on as_completed until another item is marked done (and will kick out when everything is processed)
			#result.result() captures the returned data sent by the called function in the thread (in our case runThreadedQuery)
			for result in concurrent.futures.as_completed(tasks):
				results.append(result.result()) #adds the tuple returned by runThreadedQuery to our larger list we collect here
				
				#check if enough time has passed to give an update status - if so, give a status
				report = False 
				if (startTime is None): startTime = time.time() 
				endTime = time.time()

				if((endTime - startTime) >= 60):
					startTime = time.time()
					report = True				

				if(report):
					processPID = result.result()[0]
					
					#we are going to report the failures and successes overall for this process, BUT even *reading* the global MUST use a lock, otherwise you can crash your code; so use the lock! Then unlock when done (with release)
					currentDriveLock.acquire()
					successes = threadsCompletedInCurrentDrive
					failures = failuresInCurrentDrive
					currentDriveLock.release()	
					
					print "Process {} has processed {} items (out of {}, or {}%) with {} successes and {} failures...".format(processPID, (successes + failures), len(SQL_Commands), float("{0:.2f}".format((100.*(successes + failures)/len(SQL_Commands)))), successes, failures)

		#apparently connection pools do not have a close() method?
		#cnx.close()
	else:
		print "CRITICAL ERROR - process ID {} - could not connect to database...".format(os.getpid())
		for i in range(len(SQL_Commands)): results.append([0, -1, SQL_Commands[i]])

	return results #return the collection of data (the queries that were executred, as wel las their failure or success status)



def createGenericWorkerProcesses(myMaxThreadsPerProcess = 5, maxProcesses = 5):
	"""
 	This function opens a file with multiple SQL commands in it; it divies them out among several processes, which in turn divy that subsection out among several threads
	"""
	retVal = False
	fileSuccess = 0
	
	#these are global variables - they can be seen by any process in this script. These can be viewed and set while no processes/threads are active, but as soon as there are processes/threads, they MUST be locked 
	#with the 'lock' variable (in our case that is currentDriveLock)
	global threadsCompletedInCurrentDrive
	global failuresInCurrentDrive
	
	#These can be set without the lock as there will be nothing else accessing them at this time
	threadsCompletedInCurrentDrive = 0
	failuresInCurrentDrive = 0

	#Unfortunately things that cannot be pickled by Python's pickle facility - like connections - cannot be passed across processes	
	#Since this is the case, we have to create a connection dictionary and use that to make a connection in each spawned process
	connDict = {}
	connDict['hostName'] = '127.0.0.1'
	connDict['userName'] = 'userName'
	connDict['userPass'] = 'myPassword'
	
	try:
		myFile = open("mySQL_CommandsFile.txt", "r")

		myCommands = myFile.read()
		myFile.close()

		#split on ';\n', and if the last element is '' due to this pop it (it will need to be popped)
		myCommands = myCommands.split(";\n")
		if (myCommands[len(myCommands)-1] == ''): myCommands.pop(len(myCommands)-1)

		threadResults = []

		#Launching multiple processes
		with concurrent.futures.ProcessPoolExecutor(max_workers=maxProcesses) as executor:
			tasks = []

			#find the number of items in each thread pool - round up any remainder
			numItemsInLoop = int(math.ceil((1.*len(myCommands))/maxProcesses))
			
			for i in range(1, (maxProcesses+1)):
				tempList = myCommands[((i-1)*numItemsInLoop):(i*numItemsInLoop)]

				#if we are in the last loop, just include everything
				if (i==maxProcesses): tempList = myCommands[((i-1)*numItemsInLoop):]

				if(len(tempList) == 0):
					#if for whatever reason there is nothing in the list do not spawn another process 
					pass
				else:
					#spawn the process!
					task = executor.submit(runSQL_Threads, connDict, tempList, myMaxThreadsPerProcess)
					tasks.append(task)

			#This will wait for all processes to finish - specifically the code will stall on as_completed until another item is marked done (and will kick out when everything is processed)
			for result in concurrent.futures.as_completed(tasks):
				#we want to capture the results of all SQL commands sent to the threads; unfortunately, the thread sent the calling process a list, and the calling process has a lists of lists;
				#if we try appending that to this list we would have a list of list of lists. To keep it to simply a list of lists, we use 'extend' instead of 'append' for the list threadResults
				threadResults.extend(result.result())

		#This will only be used if there actually were errors
		myMsg = "The following queries were not successfully executed:"
		numErrors = 0

		for i in range(len(threadResults)):
			if(threadResults[i][1] == -1):
				numErrors = numErrors + 1
				#print out the failures			
				myMsg = myMsg + "\n*" + threadResults[i][2]	

		if (numErrors > 0):
			print myMsg
			retVal =  False
		else:	
			retVal =  True

		print "There were {} successes and {} failures ...".format(len(threadResults) - numErrors, numErrors)

			
	#except:
	else:
		print "ERROR in createGenericWorkerProcesses..."
		return False

	return retVal

if __name__ == '__main__':
	createGenericWorkerProcesses(myMaxThreadsPerProcess = 5, maxProcesses = 5)

```
