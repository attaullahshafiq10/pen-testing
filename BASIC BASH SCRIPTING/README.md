
# BASIC BASH SCRIPTING


So we’ve got to the point where, you can do some basic enumeration and exploitation with tools such as NMap and Metasploit. Now you realise that to go any further and potentially have a crack at certifications such as the OSCP or CREST, you are going to have to go back to basics and learn a little more about scripting.

To start a bash script you are going to have to choose an editor. Bizarrely some Linux users can get awfully hot under the collar discussing their favourite editor. My advice is to try out all of them and pick the one that works best for you. I mostly use gedit and nano.

To start a bash script from the command line you start by naming the editor you will use. The ones I have discovered in wide use in my travels are (in no particular order):

gedit
nano
vi
So from the command line:

```
nano example.sh
```

This will create the empty file in your current working directory. To find out where you are from the command line simply enter pwd into the terminal.

The first line you will add is:

```
#!/bin/bash
```

Comments can be added to your script by placing a # before the comment”

```
#!/bin/bash

# This is a comment and will not be interpreted
```

Just as in the terminal echo can be used to display a line of text on the screen:

```
#!/bin/bash

# This is a comment and will not be interpreted


echo "This is a line of text that will be displayed"
echo
echo "This line will also be displayed"
```

You will notice in the above example that the there is an echo without a corresponding texting, this echo will simply add a line break.

To run this file you will first need to save the file. In nano this will be ctrl + x and then confirming to save and keep the file name unchanged, in gedit there is a save button.

Usually to launch a script you will enter ./example.sh. However at this point it is unlikely that your script will run because it does not have execute permissions.

To allow the file to be executed as a script you will need to run the following cmd:

```
chmod 755 example.sh
```

For more information on Linux file permissions click here.

You should now be able to run the bash script:

```
./example.sh
```

# PING SWEEP SCRIPT

Start a new script

```
gedit pingsweep.sh
```

Remember to include the shebang:

```
#!/bin/bash
```
Add a comment

```
# Script launched with the network portion of IP
# It will then ping each of the host portion of the network
# A list of live hosts will be presented to the user
```

To start the script we are going to add a for loop.

```
for x in `seq 1 254` ; do
```

In plain English, this says for every number between 1 and 254, do (the next line).

```
ping -c 1 $1.$x
```
ping -c 1 = ping, just once

$1 = the network portion or subnet of the network e.g. 10.0.1

.$x = means the host portion of the IP address, the x refers to the previous line where x will be every number between 1 and 254.

In effect what will happen is that you will enter (remember to chmod 755!):

```
./pingsweep.sh 10.0.1
```

The script will then ping:

10.0.1.1

10.0.1.2

10.0.1.3

10.0.1.4 and so on all the way through to 254.

In order to make this much more readable we are going to have to sort through the returns to find only the usable information.

To do this we are going to use the grep command which is essentially a search function. In the above example we are going to grep for 64 bytes and then cut out only the IP address.

First of all add the pipe | to pipe the date from the ping into the grep cmd.

```
ping -c 1 $1.$x | grep "64 bytes"
```
We are then going to add another pipe | to pass the output of the grep into a cut command:

```
ping -c 1 $1.$x | grep "64 bytes" | cut -d " " -f 4
```

-d = delimiter

cut -d  ” “ = cut the output up every time there is a space.

cut -d “,” = cut the output up every time there is a comment

-f = field

-f 1 = display only the first string (in red in the screenshot above)

-f 6 = display only the second string (in purple in the screenshot above.

We only want the live IP address so we use -f 4.

Running the script now will cycle through pinging each potential host and only return the IP addresses of live hosts:


