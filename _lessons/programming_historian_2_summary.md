---
layout: page
title: Working with Text Files in Python
description: User gains familiarity with manipulating text files in Python.
---

The stated goal of this lesson was that the user gain familiarity with manipulating text files in Python.  This lesson addressed a question I have had about when it is appropriate and / or useful to command Python to print a string with the print command.  As I learned, printing a string is useful when the information at hand requires immediate attention; otherwise, it is often useful to store this information in external files (or use it as input for additional processing) instead of printing it.  

Methods used in this lesson were “open,” “write,” and “close.”  These methods were performed on a file object named f (for the sake of simplicity).  Running this first line of code does not print a message as output but creates a file.  “Open” creates a new text file stored in the same folder as the original Jupyter Notebook, and the w stands for write (a message is being written to this new file).  Python can also get information from these files which can be accomplished with the r parameter indicating that the file being opened is being read.  The a parameter, meaning “append” opens a previously created file but adds information to it without overwriting the original contents as the “write” method does.     

Although relatively simple, this lesson demonstrates how strings can not only be printed as output, but also stored in a separate file for future revision.  This process will be useful with different datasets such as texts about to undergo natural language processing and, of course, stylometry for which this lesson is one of the preparatory exercises.  
