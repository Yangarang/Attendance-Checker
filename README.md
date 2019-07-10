# Attendance Checker
 14:332:453 Mobile App Engineering and User Experience
Mobile Applications and User Experience Final Project Report – Attendance

Contributions (Team Anonymous):
Jonathan Yang – Bluetooth Integration, SQLiteDatabase
Jan Miranda – Framework, Bluetooth Integration with Threading
Ka Wai Chu – UI Design, Excel External Saving

Jan first created the framework of the program, adding it to his GitHub and added all the
necessary activities needed for application. Ka Wai focused on the UI on how everything should
be implemented to be user friendly while Jon started with writing a SQL Database Helper class
for the Professor Activity to use when saving the student’s attendance. Ka Wai began writing the
excel external saving feature while researching other ways to save the data for perhaps easier
use, which were later scrapped due to time constraints. Jan focused on the Professor and
Student activities of bluetooth communication while Jon gave some input with bluetooth
integration, to help with integrating the database with the Professor class. One implementation
which was difficult was how to handle multiple threads when multiple student phone’s attempted
to sign in at once, which Jan focused on. Ka Wai, along with Jan, finalized all the UI design of
the application to ensure our application remained easy to use. Once we had running demo
working, we all got together and tested the application to make sure multiple signins
worked with multiple android phones. We all reviewed the code to determine any problems in
implementation. Meanwhile, Jon created the PowerPoint slides, Project Report, and
presentation goals while everyone else contributed information.. While this is mainly how we
handled each activity, we communicated with each other with any problems in design and
implementation throughout the creation of our attendance application. While this report states
the main functions of the project each member focused on, our group consistently helped one
another on all aspects of the project. As a result, everyone was involved and contributed to the
project.

MainActivity / activity_main

Determines the ‘mode’ of the app: Professor or Instructor. Also takes in a name as the input so
StudentActivity knows the name to tell ProfessorActivity. There are two buttons that start an
intent for the Student and Professor Activities.

Professor Activity / activity_professor

The layout contains an EditText to get the class name, a button to start/stop taking attendance,
a ListView, and a button to view previous Classes.
Pressing “Start Attendance” will create a bluetooth server that starts in a separate thread and
constantly accepts/rejects connections. The UUID is hardcoded in and it is the same as the on
in StudentActivity. The input in the EditText is used to create a Bluetooth device name
“MAE_ classname timestamp.”
A Class object with the input as the name is saved into the DB.
The “MAE_” prefix is used so the StudentActivity knows the device is from the same
App. When a client with the matching UUID connects to this server, a socket is created between
the two and it is handed off to another thread to handle. This allows the server to handle
connections concurrently. Through an inputStream, the Handling Thread reads the name of the
student and Creates a new Student object saving the name and MAC address. This new
student object is compared against all the other student objects already saved in the DB for this
Class session. If the new Object’s MAC Address matches another, the thread writes back to the
client “no”, otherwise it’ll write “yes” and save the Student object in the DB. Finally, the socket is
closed.
Pressing “Stop Attendance” stops the Server Thread. Every Student that Successfully in will be
listed in the ListView.
Pressing “Show Previous Classes” Will clear the ListView and populate with every class saved
in the DB. Clicking on any of the classes will launch an intent for Class Activity.

StudentActivity / activity_student

When launched it takes an extra from the intent that launched it to know the name of the
Student. When the button “Search for Classes” is pressed, it scans for Bluetooth Devices.
A BroadcastReceiver is used to know when it finds a device during the scan. When a device is
found, it’s name is checked to see if it contains “MAE_” in the beginning, if so it adds the device
to the ListView while omitting the prefix “MAE_”and adding the device to a List.
Clicking on an item in the ListView will start a Thread that will try to connect to the device. If the
connect() call works, another thread is started to manage the new socket. The Managing thread
will repeatedly send the Student’s name to the device until it receives an acknowledgement:
“yes” or “no”. If it receives and “yes”, it will Toast “Signed in” else it will Toast “Device already
signed in”. If a socket was never able to be created, it will Toast “Sign in failed”.

ClassActivity / activity_class

When launched it takes an extra from the intent that launched it to know what Class to display. It
then searches the DB for all students in that Class and populates the ListView with it.
Pressing “Export” will create an excel sheet using the jxl.jar. Every Class with the same name as
the one currently displayed takes a Column in the spreadsheet and within each column are the
students who signed into that respective class.

Database
-Models
--Student
Simple model class to hold the name and MAC address of a Student/Client
--Class
Simple model to hold the name(name+timestamp) of a Class

-- DatabaseHelper
DB Class containing SQLite commands/queries to create and search tables. There are three
tables used: one for students, one for Classes, and one to Link Students to Classes. When
creating a student, a class id is always provided so the student entry will be linked to the class
once it is created. When searching for Students, the class name is always given so only
students from a certain class are returned.
