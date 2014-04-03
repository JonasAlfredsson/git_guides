# here is some stuff

From the interactive prompt, type 5 or p (for patch). Git will ask you which files you would like to partially stage; then, for each section of the selected files, it will display hunks of the file diff and ask if you would like to stage them, one by one:

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
- 
If git presents you with a chunk larger than what you would like to add, you can use the "e" interactive command to specify the exact lines that you want added or removed.