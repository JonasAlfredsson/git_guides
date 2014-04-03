# git add -p is your friend

#### git add -p is basically "git add partial (or patch)"

This feature really improves the quality of the commits. It also makes it easy to remove parts of the changes in a file that were only there for debugging purposes - prior to the commit without having to go back to the editor.

It allows you to see the changes (delta) to the code that you are trying to add, and lets you add them (or not) separately from each other using an interactive prompt. Here's how to use it: 

from the command line, either use
- git add -p
- or you can use git add - and choose type 5 or p (for patch). 
 
Git will ask you which files you would like to partially stage; then, for each section of the selected files, it will display hunks of the file diff and ask if you would like to stage them, one by one:

![here's a visual](http://i.imgur.com/UbSnkwX.png)

At each point, you will be asked whether you want to "stage this hunk". Here are the commands you can use:
####commonly used commands
- y - stage this hunk
- n - do not stage this hunk
- a - stage this and all the remaining hunks in the file
- d - do not stage this hunk nor any of the remaining hunks in the file

####more advanced commands
- g - select a hunk to go to
- / - search for a hunk matching the given regex
- j - leave this hunk undecided, see next undecided hunk
- J - leave this hunk undecided, see next hunk
- k - leave this hunk undecided, see previous undecided hunk
- K - leave this hunk undecided, see previous hunk
- s - split the current hunk into smaller hunks
- e - manually edit the current hunk
- ? - print help


#### Some cool tips from the internet: 

- If git presents you with a chunk larger than what you would like to add, you can use the "e" interactive command to specify the exact lines that you want added or removed. This is probably the most powerful option. As promised, it will open the hunk in a text editor and you can edit it to your hearts content
- Split the hunk into smaller hunks. This only works if there’s unchanged lines between the changes in the displayed hunk, so this wouldn’t have any effect in the example above
- git reset -p works in a similar way
- git commit -p it combines git add -p and git commit in one command.


