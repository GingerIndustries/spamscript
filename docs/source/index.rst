.. SpamScript documentation master file, created by
   sphinx-quickstart on Tue Apr 27 14:44:05 2021.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

######################################
Welcome to SpamScript's documentation!
######################################

.. toctree::
   :maxdepth: 2
   :caption: Contents:


###################
What is SpamScript?
###################
SpamScript is an inline programming pseudo-language, like AWK and Regex, that is used to produce dynamically formatted text.

######################
Language Specification
######################

***********
Terminology
***********
In this specification, certain pieces of terminology are used that will be explained here.
*	ss: The command-line tool to run SpamScript programs.
*	pyss (pronounced pie ess ess, not pess and DEFINITEY NOT PISS): The Python module to run SpamScript programs from inside Python scripts
*	print: To add text to the output or to “type” text on the keyboard.
*	Activator: Generally refers to the + sign. See Symbols & Grammar for more info.

***************
Part 1: Headers
***************
A SpamScript header always goes at the very start of a SpamScript program, and looks like this: 
``[default]``
Headers are formatted with the mode as one word in the front, and flags following it separated by spaces, with arguments at the end. (See Arguments for more info.)
``[mode -flag value -flag value -flag value … -flag value args ARG1 ARG2 ARG3]``
Here’s a functional example. This header specifies the string mode, the Python version flag, the Strict Arguments flag, and that the first argument should go into a variable named “kermit”.
``[string -py 3.7 -sa args kermit]``

Arguments
=========

At the very end should be the word “args” followed by the variables that command line arguments get put into. An argument variable named “ARGS”, in all caps, will receive all other (unassigned) arguments as a list. It should only be put at the very end, as any variables after it will contain copies of its contents. (i.e. if the below example had an ARGS, it would contain all arguments AFTER argument 3). All extra arguments will be discarded if this variable is not included, UNLESS the -sa flag is passed. See Flags for more info.

Modes
======
*	``string``: This mode will run the entire program and return its complete output at the end. Wait statements will be ignored, and infinite loops will stop the program with an InvalidStatementException. Also, MOST keys that aren’t letters will be excluded from the output.
*	``live``: This mode must be specified with the -o flag. It will use the specified output library to press the keys on the keyboard according to the program.
*	``default``: This mode will work the same as the default mode (usually string).

Flags
======
*	``-o``: This flag can only be used with live mode and must be the FIRST flag. It specifies which input library SpamScript should use. Values:

  *	Input
  * Pyautogui
  
*	``-py``: This flag specifies the version of the Python interpreter ss should use when running the program. Values can be any number that is 3 or more (SpamScript does not support Python 2.x)
*	``-v``: This flag will make the program print out the version of ss/pyss it is using.
*	``-pv``: This flag will make the program print out the version of the importer of pyss. If the version is not specified, it will work the same as -v.
*	``-d``: This flag will enable debug mode. Check Debugging for more info.
*	``-sa``: This flag enables Strict Arguments mode. If this flag is passed, extra arguments will not be silently discarded; Instead, the program will stop with an error telling the user if they passed too many or too few arguments. NOTE: The warning about extra arguments will not appear if ARGS is specified. (See Arguments for more info.)

Gotchas
=======

There are a few caveats and things that you should know related to headers.

Not specifying headers
----------------------

Headers are advised, but not required. Not specifying a header or specifying an empty header ([]) works the same way as specifying this header:
``[default args ARGS]``

A word about ``[default]``
--------------------------

Generally, you do not want to specify ``[default]`` as a header. You may think that it will work the same as no header, but it actually specifies the mode and nothing else, therefore keeping args from working. It’s not the end of the world, but no header or empty headers are generally a better option.

Sneaky sneaky
-------------

One of the most common causes of an InvalidHeaderError is when you have text that starts with a square bracket. The SpamScript interpreter thinks you’re specifying a header and loses its mind when you instead have normal text. The best way to fix this is to put an empty header at the beginning of your program, i.e. ``[]``. 

Can I change default?
---------------------

Yes, but you really shouldn’t. See Configuration for more info.

What can go before the header?
------------------------------

The only thing that should go before a header is a comment. End of story.




Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
