# here is some stuff

From the interactive prompt, type 5 or p (for patch). Git will ask you which files you would like to partially stage; then, for each section of the selected files, it will display hunks of the file diff and ask if you would like to stage them, one by one:

![here's a visual](http://i.imgur.com/UbSnkwX.png)

Stage this hunk [y,n,a,d,/,j,J,g,e,?]? ?
- y - stage this hunk
- n - do not stage this hunk
- a - stage this and all the remaining hunks in the file
- d - do not stage this hunk nor any of the remaining hunks in the file
- g - select a hunk to go to
- / - search for a hunk matching the given regex
- j - leave this hunk undecided, see next undecided hunk
- J - leave this hunk undecided, see next hunk
- k - leave this hunk undecided, see previous undecided hunk
- K - leave this hunk undecided, see previous hunk
- s - split the current hunk into smaller hunks
- e - manually edit the current hunk
- ? - print help

If git presents you with a chunk larger than what you would like to add, you can use the "e" interactive command to specify the exact lines that you want added or removed.

_____

At the top is some diff info about the current position in the file, followed by the actual diff of the source code (called “hunk”), and below that are your available options. Pressing ? will give you a short explanation for each one, but the ones I use most often are:

y - Yes, add this hunk
n - No, don’t add this hunk
d - No, don’t add this hunk and all other remaining hunks. Useful if you’ve already added what you want to, and want to skip over the rest
s - Split the hunk into smaller hunks. This only works if there’s unchanged lines between the changes in the displayed hunk, so this wouldn’t have any effect in the example above
e - Manually edit the hunk. This is probably the most powerful option. As promised, it will open the hunk in a text editor and you can edit it to your hearts content

-----------

ust as the author describes, this feature really improves the quality of the commits. It also makes it easy to remove parts of the changes in a file that were only there for debugging purposes - prior to the commit without having to go back to the editor.

_____________

FYI `git reset -p` works in a similar way