# shell介绍
先介绍几个专业术语

Term	| What it is
--- | ---
shell	| what we use to talk to the computer; anything where you are pointing and clicking with a mouse is a Graphical User Interface (GUI) shell; something with text only is a Command Line Interface (CLI) shell
command line |	a text-based environment capable of taking input and providing output (a "terminal" is the same idea)
Unix	| a family of operating systems
bash	| the most common programming language used at a Unix command-line

## 为什么要学命令行
* Many bioinformatics tools can only be used through a command-line interface, and/or have extra capabilities in the command-line version that are not available in the GUI (e.g. BLAST).
* It makes our work less error-prone. When humans do the same thing a hundred different times (or even ten times), we’re likely to make a mistake. Your computer can do the same thing a thousand times with no mistakes. This also frees us up to do other things we can’t automate, like the science part.
* The shell makes our work more reproducible. When we carry out our work in a command-line interface (rather than a GUI), we can keep an exact record of all steps, which we can use to re-do our work when we need to. It also gives us a way to unambiguously communicate what we’ve done to others.
* Many bioinformatic tasks require large amounts of computing power and can’t realistically be run on our own machines. These tasks are best performed using remote computers or cloud computing, which can only be accessed through a shell.
