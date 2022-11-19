# Work on week #8


## Task 1

* The main objective of this task is to find the profile information of Alice, who is an employee that develops the website.

* Firsly we need to start the container that connects the website with the server. In order to do that, we ran the command ```dcup```:

![](https://i.imgur.com/DRgWrHR.png)

* After that, we needed to find out what was the ID of the database's container. In order to that, we ran the command ```dockps``` and we found out what we wanted!

![](https://i.imgur.com/TlQrzqb.png)

* After that, we started a shell of that container, thanks to the command ```dockhs 95```, where, '95' is the ID of database's container. Also, we ran the command ```mysql -u root -pdees``` to start a mysql program, since our database was made with MySQL:

![](https://i.imgur.com/BKWMiTE.png)

* Then, we ran the commands ```use sqllab_users;``` and ```show tables;``` in order to find out which tables existed in the database. We realized that there is one table called 'credential':

![](https://i.imgur.com/R2q5P0A.png)

* After that, we ran the command ```SELECT * FROM credential;```, so we could know which attributes existed.

* So, if we wanted to know the profile information of Alice, we simply ran the command ```SELECT * FROM credential``` and we found out all the content of credential and a special attribute called 'Name':

![](https://i.imgur.com/JCECy05.png)

* So, if we wanted the profile info of Alice, we simply ran the command ```SELECT * FROM credential WHERE Name = 'Alice';``` and we obtained a row with her profile information:

![](https://i.imgur.com/BOPCWAL.png)


## Task 2

### Task 2.1

* The goal of this task is to obtain information of all the employees, my making a SQL Injection Attack from the webpage. We already know that the administrator's account name is "Admin", but we do know the password, since it is encrypted. We can obtain this information by making the command ```SELECT * FROM credential;``` in the database:

![](https://i.imgur.com/R2F9XkW.png)

* We analysed how we the request is done, and we knew we can inject SQL code in it:

![](https://i.imgur.com/dv3mibA.png)

* However, our attack isn't compromised at all: we can simply make a SQL Injection Attack by comment the password part in the username input (with '-- ', right after writing the correct input of the username, which is 'Admin'. In order to do that, we wrote this in the UserName input of the website:

![](https://i.imgur.com/megCfDE.png)

* Finally, we obtained all the information about the employees:

![](https://i.imgur.com/Ij982RS.png)

### Task 2.2

* In this task, we have to do the same thing of the previous one; however, we have to use a different method- this time, we have to make the SQL Injection Attack from the command line. We can do it by using the command ```curl``` and use the URL of the website. However, we need to put the correct values in the request parameters ('username' and 'Password').

* In the username, we can use the input of the previous task: "Admin'-- ". However we have just to switch the "'" and the white space to %27 and %20, respectively. So, our input in the username parameter will be "username=Admin%27--%20". Since we didn't use the password input in the previous, we can ommit it in this one as well.

* So, our URL will be 'http://www.seed-server.com/unsafe_home.php?username=Admin%27--%20'. We ran the command ```curl``` with this link and we obtained a response containing the information of the database:

![](https://i.imgur.com/RQy88YV.png)


### Task 2.3
* This task is impossible because stacking queries is not allowed in mysql with php as shown by this article:
![](https://i.imgur.com/Ivg7mdl.png)

## Task 3

### Task 3.1


* In this challenge, we have to modify the salary of Alice. Firstly, we need to see how is the variable acessed. In order to do that, we made this query and we found the information we needed:

![](https://i.imgur.com/4kP6Usj.png)

* After that we logged in with the credentials of Alice. Since we do not know her password, we simply used the technique to log in as an administrator. So we put in the username the string "Alice'-- " and we logged in successfully:

![](https://i.imgur.com/7aazmHc.png)

![](https://i.imgur.com/5PzhPLl.png)

* To change her salary, we explored the 'Edit profile' page, which has also a form:

![](https://i.imgur.com/VDwvkpm.png)


* After we analysed the respective PHP code of this page, we found out that we could do a SQL Injection Attack on the UPDATE:

![](https://i.imgur.com/2FI8Zeg.png)

* We could choose a parameter of the edit page, add the salary collumn and inserted the value we wanted. In order to do that we inserted in the phone number parameter the string "', Salary='1000000000". We did in that way, since we did not need to modify phone number's collumn.

![](https://i.imgur.com/tPfflOt.png)

* After saving the changes, we successfully modified the Alice's salary.

![](https://i.imgur.com/EoC7BYc.png)


### Task 3.2

* This challenge is preety similar of the previous one- we needed to change the boss' salary to 1 dollar. In order to that, we used the same strategy in the 'Edit profile' form.

* In the Alice's "edit profile" page, instead of inserting the "', Salary='1000000000", we put the string "', Salary = 1 WHERE Name = 'Boby';-- " in the phone's number parameter, for instance. We did that to update in the Bobby's row and ignore the WHERE statement of the PHP file:

![](https://i.imgur.com/SXVuBgz.png)

* Finally we logged in with the credentials of the administrator to confirm the exploit and, fortunately, we did it:
![](https://i.imgur.com/7ujW9kR.png)

## CTF 
 
### Challenge 1

* In this CTF challenge,first we explored the code provided and we saw how the query to login was made:

![](https://i.imgur.com/amrvSNs.png)


* From that query we could just use to login as admin on task1,using "admin';#",and put on the password "#" just to comment a password.

![](https://i.imgur.com/24v6lzd.png)

* And we got the flag.