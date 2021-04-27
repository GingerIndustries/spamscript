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

***********************
Part 2: Language syntax
***********************

Now for the fun part! This section details how to write code in SpamScript. There are 3 “parts of speech” in SpamScript: Text, Statements, and Storage, as well as the language’s symbols (like grammar).

Text
====

Text is the most fundamental part of SpamScript, and the easiest part of it to learn and understand. Normal text in programs will be printed as it is. For example, ``Hello world!`` will print “Hello world!” verbatim to the output. However, there are some keys that can’t be represented with words, like enter, backspace, tab, and F7. These are represented with “specials”. A special is formatted like this:
``+”keyname”``
The operator goes at the beginning, and the name of the key is enclosed in single or double quotes. A full list of specials is accessible with the ``keylist`` statement.

Statements
A statement is an instruction to SpamScript to do something special, and is what makes SpamScript an actual language. Statements are formatted like this::

  1.	+[name mode]
  2.	+[name mode (arg1 arg2 arg3 …)]
  3.	+[name mode (arg1 arg2 arg3 …)] {code}

The name is obviously the name of the statement, and the args its arguments. The mode is the mode of the statement, which makes the statement’s function more specific (Some statements have just one mode, and as such do not require the mode text).  Most statements are formatted like #2, with some like #1 and a select few like #3. Examples of each type::

  1.	+[time]

    a.	+[time date]

  2.	+[time asstr (%H : %M %S)]
  3.	+[loop iterations (3)] {Hello}

    a.	+[loop forever] {Hello}

Storage
=======

Storage is the final part of SpamScript, and arguably the most powerful. It has two parts: variables and functions.
Variables
Variables store data, and are formatted like this::

  1.	+{name value}
  2.	``+{name}``

#1 stores data in a variable, and #2 retrieves it. Example:
+{myvar 120} myvar is: +{myvar} myvar ++ 1 is: +[math add ({myvar} 1)]
Output: myvar is: 120 myvar + 1 is: 121
Functions
Functions are technically a kind of statement, but are here because of what they do. Functions are formatted like this::

  1.	+[func name (mode, param1, param2, …)] {code}
  2.	+[name mode (parameters)]

Functions work exactly like statements when called. When defined, name is the function’s name, and the parameters are the arguments, as well as the mode. The mode parameter is optional, but if specified it must be first. When a function is called, the code inside its block will run, and the result will be returned. Functions always run as if they were in string mode, regardless of actual mode. Example::

  +[func myFunc (mode, someNumber)] {Mode is: +{mode}. someNumber is: +{someNumber}, and someNumber * 2 is +[math multiply (+{someNumber} 2)].}I will now call myFunc. Results of myFunc: +[myFunc someMode (123)]

Output: I will now call myFunc. Results of myFunc: Mode is: someMode. someNumber is: 123, and someNumber * 2 is 246.

Symbols & Grammar
=================

SpamScript uses certain symbols to indicate parts of speech. This section provides a list of symbols.

+ is the “activator”, a symbol that always comes before an enclosure or an operator. To print a literal +, type ++.

[], (), {}, ‘’, and “” are all enclosures. They come after the activator and contain relevant data.
•	[]: Enclosure for statements and headers
•	(): Enclosure for arguments
•	{}: Enclosure for variables and code blocks
•	‘’ and “”: Enclosures for specials

~, ~\*, \*~, and / are all operators. They come after the activator, and have relevant data following them.
•	~: A single-line comment. ``+~ This is a comment``
•	~\*: Begins a multi-line comment.
•	\*~: Ends a multi-line comment.
::

  ~*
  This is
  A multiline
  Comment
  *~

/: Represents a newline. In string mode it will be an actual newline, in live mode it will be a press of the Enter key.  ``Line one+/Line two``

***********************
Part 3: Actual examples
***********************

This section will provide examples and explanation to assist with learning SpamScript.

Simple examples
===============



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
