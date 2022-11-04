# Work on week #7


## Task 1

* Our goal in this task is to provide an input to the server and when the server program tries to print out the user input in the myprintf() function,it will crash.If myprintf() returns,it will print out "Returned properly" as you can see in our image below. 

* In this task will use a command **$ echo hello | nc 10.9.0.5 9090**  to send a string "Hello" to a function myprintf() on the server 10.9.0.5.

![](https://i.imgur.com/aojyVHP.png)
 <p> 
    Fig.1- Output of the function myprintf() and more ...
</p>

## Task 2

### Task 2A

* In order to complete this task, we need to understand the printf works, in order to expose sensitive data from stack. Firstly, we can put some 4 B in the code and see the next variable's adress. We can use the 4 B bytes given. However, we have to change the format string in order to print the variable successfully.

* Since we want to print 4 B and each has 8 bits, we need to print 64 (4*8) registers, the 63 before and actual register. So we alter the string ```s``` to this format.

![](https://i.imgur.com/S9KoCkA.png)

* Then, we need to compile this program, using these commands:

![](https://i.imgur.com/CapMC8z.png)

* And we have the expetec result. Since we can see our bytes selected before the variable's address: 

![](https://i.imgur.com/k21HiCO.png)


### Task 2B

* Now we know what is the next address. So we can use it to know what is the content of the heap.

* Firstly, we set the value of the variable ```number``` to the secret address, which is given by the server (0x080b4008)

* The difference between this task and the previous one is that now we want the access the content of the address, not the address itself and we know this content is in the string format, So instead of read 64 registers, we read 63 and the last on we use the '%s' format to read the content of the register. So, this is build_string.py now:

![](https://i.imgur.com/Zjx3Oc1.png)

* We execute the same commands and we obtained the secret message:

![](https://i.imgur.com/bW0vxXF.png)


## Task 3


### Task 3A


 In this task we need to change ```number``` variable.
 
 * Like we have done in task 2B, we have to access the address by making it the beginning of our string into it. The address is 0x080e5068.
 
 * We can access it by using "%x" 64 times and changing the values for 63 times plus "%n".The reason for this is the same we used to crash the server and we can write to the address the pointer is pointing to.In this case pointing to where the secret variable was located.
![](https://i.imgur.com/maqsOPh.png)



![](https://i.imgur.com/93MIqlT.png)


![](https://i.imgur.com/YgS2aLu.png)


### Task 3B

The difference between task 3B and 3A is in this one we have to change the secret variable like task 3A but also change it to a specific value: 0x5000

* We know "%n" print the number of characters printed in the string so far, and it will be able to write 0x5000. We must make it so that our string has 5000 characters before it. 

 
![](https://i.imgur.com/BbsP66U.png)

![](https://i.imgur.com/MCGAj9i.png)

* From this task we understand how printf() is used to acess or change memory from an attacked device, how it can be exploited and what cautions we need to have when using it.

* We have also learned how an attacker can use the side effects of "%x", "%s", "%n" in a formatted string to his/her advantage performing an attack.
