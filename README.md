# google_api-and-sheets
Skip to main content
logo.png


Register now for the free LibreFest conference on October 15

Search 
How can we help you?
 Search
Remixer
Downloads
Username
  
Password
  Sign in
Sign in
Contents Home   Campus Bookshelves   Intercollegiate Courses   Internet of Science Things (2020)   1: IOST Modules Expand/collapse global location
1.6: Writing to Google Sheets
Last updatedApr 20, 2020
1.5: GPIO Inputs
 
1.7: Measuring Temperature
picture_as_pdf
Readability
Cite this page
Donate
Belford.jpg
Contributed by Robert Belford
Professor (Chemistry) at University of Arkansas at Little Rock
Table of contents
hypothes.is tag:
Download Assignment:

Learning Objectives
Students will be able to:

Content:

Use Google Sheets to store data from remote source
Set up Raspberry Pi to send data to Google Sheet
Process:

Set up Google Sheet to receive external data
Set up Raspberry Pi to send data to Google Sheet    
Prior Knowledge

Python and IoT concepts from previous Activities
Further Reading

https://console.developers.google.co...=python-222921
http://faradaysclub.com/internet-of-things-bme280-datalogger-with-raspberry-pi-using-gspread/
Task 1: Setting up a Google Sheet to accept raspberry pi data
Suppose you want your RasPi to save collected from a program/sensor to a Google Sheet that you can publish on the web or share the link. Google has set up an API (Application Programming Interface) to allow you to do that.

To set this up:

1. Go to sheets.google.com and log into the google account you set up in Activity 3a for gmail. Click on start a new blank spreadsheet.

Create new Google Sheet
Figure  1.6.1 : Create new Google Sheet
When the sheet is created, you should have an “Untitled Spreadsheet”

Untitled Spreadsheet
Figure  1.6.2 : Untitled Spreadsheet
In the section where it says “Untitled spreadsheet” give it a name. For this activity, let’s call it Raspi_data

Give a name to your spreadsheet
Figure  1.6.3 : Give a name to your spreadsheet
In the first row create a column headers of Datetime and data

Create a column headers
Figure  1.6.4 : Create a column headers
2. Open a new tab and go to the Google api console http://console.developers.google.com

You will be creating a new project.

Select a project
Figure  1.6.5 : Select a project
 

New Project
Figure  1.6.6 : New Project
In the New project window, you will see a place to type project name. Give it the name mydata

Notice that you have a project ID that automatically is generated. Write this name and number down. You don’t need to change the location.

Write down the name and number
Figure  1.6.7 : Write down the name and number
Click create. You will see something indicating your project is being created:

Click Create
Figure  1.6.8 : Click Create
Click the reload icon on the google browser

Reload the page
Figure  1.6.9 : Reload the page
Next to the Google APIs header, select mydata from the dropdown menu to select this project.

Select your project
Figure  1.6.10 : Select your project
The myproject project should be in the header for Google APIs:

Myproject project should be in the header
Figure  1.6.11 : Myproject project should be in the header
Click on Enable APIS and SERVICES:

Enable APIs and Services
Figure  1.6.12 : Enable APIs and Services
In the search window, type sheets, and you should see the following:

Search for sheets
Figure  1.6.13 : Search for sheets
Click on the Google Sheets API that comes up as the result. Then click the ENABLE button.

Enable Sheets API
Figure  1.6.14 : Enable Sheets API
It will indicate that it is enabling the API and the following scree will come up. At this point, notice that you have mydata as the active project (1), and then you will click on the “CREATE CREDENTIALS” tab (2).

Click Create Credentials
Figure  1.6.15 : Click Create Credentials
First you need to choose the API you are using. From the dropdown menu, choose the Google Sheets API:

Choose Google Sheets API
Figure  1.6.16 : Choose Google Sheets API
Now the screen will prompt you for where you will be getting data from. In this box you will choose “Other non-UI (e.g. cron job, daemon) and the data type is application data and not using a compute engine, then click what credentials do I need.

Make changes as indicated above
Figure  1.6.17 : Make changes as indicated above
You will now be adding credentials to your project:

Fill in the service account name. Use MyRasPi

Select Role: choose project \ Owner

Key Type is JSON.

Add credentials to your project
Figure  1.6.18 : Add credentials to your project
Make a note of your service account ID as well. Then click continue. You will get an automatically downloaded JSON key that you need to use later. It will be in your downloaded files folder. Do not lose this.

You now have set up the credentials for this account.

Credentials
Figure  1.6.19 : Credentials
 

JSON key will automatically download 
Figure  1.6.20 : JSON key will automatically download 
Using a text Editor, open the JSON downloaded file. You will need the email address called client_email.

Copy client_email
Figure  1.6.21 : Copy client_email
Copy that email address into your clipboard (using Crtr-C) because we will need for the google sheet.

Go back to your Google sheet (Raspi_data) and click on file / share or click on the green share button in the upper right side of screen.

Click Share
Figure  1.6.22 : Click Share
You will then paste that email address into the share email, then click send.

Paste client_email here
Figure  1.6.23 : Paste client_email here
You will need to copy that JSON file into the directory where you write your python programs on your raspberry pi. You should have been using /home/pi/Python_programs or something similar. Copy the JSON file that you downloaded to that directory. You will need to have access to it from the python programs that you write that will access the google sheet. The file that I downloaded was called mydata-7ad604e1aaef.json   when I put it in my python programs folder, I renamed it to mydata.json so that I could easily reference it later.

There is one last thing to enable on Google. Go back to the console:

http://console.developers.google.com

Select your mydata project. Click on ENABLE APIs and SERVICES again.

Click on Enable APIs and Services again
Figure  1.6.24 : Click on Enable APIs and Services again
This time in the search window, fill in drive and select the Google Drive API:

Select Google Drive API
Figure  1.6.25 : Select Google Drive API
This will bring up a new page that will allow you to enable the Google Drive API.

Click Enable
Figure  1.6.26 : Click Enable
You will need both the Google Drive API and the Google sheets API enabled to be successful in writing to the google sheet. Since you already created credentials for the Sheets API, you should not need new credentials for the Google Drive API.

If you click on the GoogleAPIs logo again, and select project mydata, you should see at the bottom of the screen that both the APIs are enabled:

Make sure both the APIs are enabled
Figure  1.6.27 : Make sure both the APIs are enabled
3. Install some software on the RasPi

Now we have to install the google spreadsheet and authorization client python modules. Since we are using python3, we need to use pip3 as our package manager. Open a terminal window and type the following:

pi@hostname:~ $sudo apt-get update
pi@hostname:~ $sudo apt-get upgrade
pi@hostname:~ $sudo pip3 install gspread oauth2client
pi@hostname:~ $sudo pip3 install -–upgrade google-auth-oauthlib
Task 2: Enter the following program on your Raspberry pi
Save it to your Python_programs directory as test_sheets.py and make sure the mydata.json file is in the same directory as this program.

Task 2 Python Program
Python Program 1
import datetime, time
import gspread
from oauth2client.service_account import ServiceAccountCredentials
 
scope = ['https://spreadsheets.google.com/feeds','https://www.googleapis.com/auth/drive']
creds = ServiceAccountCredentials.from_json_keyfile_name('mydata.json', scope) 
#replace mydata.json with the name of your data file
client = gspread.authorize(creds)
sheet = client.open("Raspi_data").sheet1
 
time = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f')
print(time)
data = 1
values =[time,data]
sheet.append_row(values)
 Critical Thinking Questions:

Before running the program above figure out what each line of code is supposed to do when the program run.
Run the program a few times, and check your google sheet called Raspi_data. What have you accomplished with Task 1 and Task 2 today?
Task 3: Enter the following program on your Raspberry pi
Save it to your Python_programs directory as test_sheets2.py and make sure the mydata.json file is in the same directory as this program.

Task 3 Python Program
Python Program 2
import datetime, time
import gspread
from oauth2client.service_account import ServiceAccountCredentials
 
scope = ['https://spreadsheets.google.com/feeds','https://www.googleapis.com/auth/drive']
creds = ServiceAccountCredentials.from_json_keyfile_name('mydata.json', scope) 
#replace mydata.json with the name of your data file
client = gspread.authorize(creds)
sheet = client.open("Raspi_data").sheet1
 
data = 1
 
def append_sheet(data):
    time = datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S.%f')
    print("*"*80)
    values =[time,data]
    print(values)
    sheet.append_row(values)
    print("Google Sheet Updated")
    print("*"*80)
 
while data<10:
    client.login()
    append_sheet(data)
    data += 1
    print("Waiting")
    for i in range (10):
        print(i)
        time.sleep(1)
3. Before running the program above figure out what each line of code is supposed to do when the program run.

Back to top
1.5: GPIO Inputs 1.7: Measuring Temperature
Was this article helpful?YesNo
The LibreTexts libraries are Powered by MindTouch® and are supported by the Department of Education Open Textbook Pilot Project, the UC Davis Office of the Provost, the UC Davis Library, the California State University Affordable Learning Solutions Program, and Merlot. We also acknowledge previous National Science Foundation support under grant numbers 1246120, 1525057, and 1413739. Unless otherwise noted, LibreTexts content is licensed by CC BY-NC-SA 3.0. Legal. Have questions or comments? For more information contact us at info@libretexts.org or check out our status page at https://status.libretexts.org.

CSU ALS Logo
DOE Logo.png
UC Davis Logo
Merlot Logo
NSF Logo.png
UC Davis Library Logo
