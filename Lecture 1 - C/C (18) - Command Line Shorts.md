🐧 CS50x Week 1 — Linux Command Line (Doug Lloyd)

1) What is the “command line” in CS50?
- “The CS50 IDE… is a cloud-based machine which runs Ubuntu, which is one of the many flavors of the Linux operating system.”
- “Linux… is favored by programmers, because it’s just cooler, right?” 😄
- 🖱 GUI vs ⌨️ Terminal: Many Linux distros have GUIs (“easy mouse-based navigation”, “double-clicking on icons”) but “you’ll still be using your terminal window pretty frequently” and “you can do many of the same tasks… with keyboard commands”.
- 🧬 Unix-based systems note: “These commands can be used on any Unix-based operating system… includes Linux… also includes Mac OS.” Windows: “Command Prompt… some commands are slightly different… because Windows is not a Unix-based system.”

2) The CS50 IDE layout (what you’re seeing)
- “On the left… a file tree” / “At the center… where you’re going to be writing your code” / “At the bottom… a terminal window”.
- Example contents: Two folders: pset0, pset1. Three files: hello, hello.c, hello.txt.

3) ls — list
- ✅ What it does: “The first one… is ls… short for list.” “It gives you a readout of all the files and folders in your current directory.”
- 🧪 Example output: hello  hello.c  hello.txt  pset0  pset1.
- 🎨 Color clue: “pset0 and pset1 are colored blue… directories”; “every other thing is colored black… text/source”; “green… executable file”; “That means that I could run a program called hello.”

4) cd — change directory
- ✅ What it does: “The next command… is cd… short for change directory.” Lets you “navigate between directories… as opposed to double-clicking on folders.”
- 📌 Special names: current = “.” ; parent = “..”.
- 🧪 Navigate into a folder: `cd pset1` → prompt shows ~/workspace/pset1 → `ls` → “I see some different stuff here… I’ve navigated into pset1.”
- 🧼 Clear screen: “control l… clears the screen.”
- 🧪 cd . : “cd space dot… doesn’t do anything… changing directories to the current directory.”
- 🧪 cd .. : “cd space dot dot… now… I’m back in my ~/workspace directory.”

5) pwd — present working directory
- ✅ What it does: “If you’re ever curious about the name of your directory, you can type pwd.” “It tells me exactly where I am.” `/home/ubuntu` = “the long way of saying tilde” (“tilde… is your home directory”).

6) Fast travel: cd (with nothing)
- “If I ever want to get back to just my home directory, tilde, I can type cd with nothing after it.”
- Then: “If I want to get to workspace, I can just type:” `cd workspace`.

7) mkdir — make directory
- ✅ What it does: “mkdir… short for make a directory… create a new folder.” GUI equivalent: “right-click… choose New Folder”.
- 🧪 Example: `mkdir pset2` then `ls` → “now I have a pset2 folder.”

8) cp — copy (file + directory)
- ✅ What it does: “cp… short for copy.” “takes two arguments… a source… and a destination.”
- 📄 Copy a file: `cp hello.txt hi.txt` then `ls` → “there’s hi.txt… I made a copy of it.”
- 📁 Copy a directory (needs -r): “I can’t just say cp pset0 pset3… you get… omitting directory pset0.” Use “-r for recursive” → `cp -r pset0 pset3` → pset3 contains “sample and scratch.sb2 again.” Reminder: “use the -r flag…”

9) rm — remove (danger zone)
- ✅ What it does: “rm… for remove… name of the file.” “rm is very careful… no Recycle Bin here… once we delete a file, it’s gone.”
- 🧪 Remove a file (with confirmation): `rm hi.txt` → “Do you really want to remove… hi.txt?” → yes/y → `ls` shows it’s gone.
- ⚡ Force delete: `rm -f hello.txt` — “It’s gone. Didn’t even ask me the question.” “There’s no undo here.”
- 🧨 Delete a directory: `rm -r pset2` — prompts; answer yes; directory gone.
- ☢️ The nuclear combo: `rm -rf pset3` — “use it sparingly… with caution.” “Didn’t even ask me… pset3 is gone.”

10) mv — move (rename)
- ✅ What it does: “mv… short for move.” “Move is basically equivalent to rename… source to destination.”
- 🧪 Example: “I was working on my greedy problem, but I accidentally named it greddy.” Instead of copy+remove, do one command: `mv greddy.c greedy.c` (rename in place).

11) Quick Summary — the “Big 5” commands
- “These five commands should get you pretty far…”
- 📃 ls — list files/folders
- 🧭 cd — change directory (., .., and cd to home)
- 📁 mkdir — create folder
- 🧬 cp — copy (-r for directories)
- 🗑️ rm — delete (-f force, -r directory, -rf dangerous)
- 🚚 mv — move/rename
