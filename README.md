# CMPS 3560 Lab Manual
# Propositional Logic Forward Chaining: An introduction

# Objectives

* Intro to python3
* Intro to propositional logic (PL)
* Implement naive implementation of forward chaining (FC) to solve a problem

# Prerequisites Topics

* Propositional logic syntax, semantics, connectives. (Russel and Norvig 3e Ch. 7.4.)
* Horn clauses (Russel and Norvig 3e Ch. 7.5.3.)
* Forward chaining in propositional logic

# Introduction

The goal of this lab is to introduce you to `python3`-–the primary language of this class-–by implementing a simple, unoptimized forward chaining algorithm to solve a propositional logic problem. Specifically, we will use artificial intelligence to solve the duck test:

```
Looks ∩ Swims ∩ Quacks => Duck,
Barks => Dog,
Hoots ∩ Flies => Owl,
Looks,
Swims,
Quacks.
```

If it looks like a duck, swims like a duck, and quacks like a duck, then it probably is a duck. The subclauses/facts at the end support the conclusion that it is, in fact, a duck. There are a few more rules thrown in there: if it barks its a dog, if it hoots and flies it’s an owl. Indeed, by the powers of our logical deduction: 

```
Looks ∩ Swims ∩ Quacks => Duck, Looks, Swims, Quacks |= Duck
```

This is something we can tell at a glance. But, the goal of the lab is to have a computer automatically come to this conclusion.

## Forward Chaining

Forward chaining (FC) is an algorithm for inference that iterates exhaustively over each rule in the KB attempting to entail new sentences with modus ponens. The algorithm stops when no new knowledge can be generated. It is generally described as:

```
do
  for each rule
    rule = (antecedent, consequent)
    if antecedent is satisfied 
    and consequent is not in the KB
      insert consequent into KB
    endif
  endfor
while something new inserted in KB
```

Note that this is pseudo-code not `python3`. If we apply this to the duck test:

### Iteration 1

Iterating over the KB:

```
Looks ∩ Swims ∩ Quacks => Duck, Looks, Swims, Quacks |= Duck,
Barks => Dog, ¬Barks |≠ Dog,
Hoots ∩ Flies => Owl, ¬Hoots, ¬Flies |≠ Owl
```

Duck was inserted to the KB. However, FC is goal less. Note that there is no mention of our goal in the pseudo-code. So inference will continue until no new knowledge can be generated.

### Iteration

Iterating over the KB:

```
Barks => Dog, ¬Barks |≠ Dog
Hoots ∩ Flies => Owl, ¬Hoots, ¬Flies |≠ Owl
```

The first rule was omitted because the consequent was already inserted into the KB. Nothing was inserted in the KB so we terminate. The goal was inserted into the KB in iteration 1, so it is entailed. Next, we will briefly cover the language we will use to implement this algorithm.

## Python 3

It is overkill to use AI in this scenario, so the goal of this lab is really to introduce you to `python3` and some if its constructs. If using Linux or Mac, `python3` is the CLI command for Python 3. You do not need to use `odin.cs.csubak.edu` for this lab:

* If you have Linux:
** You can use a departmental server, it should have `python3`
** Install Python 3 on your machine
*** Debian or Ubuntu: `sudo apt update && sudo apt upgrade ** sudo apt install python3`
*** CentOS or RHEL: `yum update -y` then `yum install -y python3`
* If you have Windows:
** Run it within Windows. Install Python 3 (Google this) and you can now run `python3` in a terminal of your choice, use the PowerShell terminal for Python 3
** Install Windows Subsystem for Linux and follow the instructions for Linux. This step runs an emulated Linux on top of your Windows.
** Install Cygwin, and select the Python 3 package during installation, use the Cygwin terminal for Python 3. This step runs a simulated Linux terminal on top of your Windows--it is not Linux it only appears to be Linux.
* If you have Mac:
** Install Python 3 for Mac (Google this), and then use the terminal for Python 3

**Make sure you are using Python 3 and not Python 2!**

It is a high-level  general-purpose  scripting  language.   Its  recommended  that  you  use  this  language  sothat the concepts of memory management, types and arrays are abstracted out of your codeso you can focus on the theory of the algorithms.  If you’re using a departmental machine itmost likely haspython3installed.  You can check the version with:$ python3 -VPython 3.6.4Note  that  we’re  usingpython3and  notpython,  they  are  two  different  things!Youroutput may vary based on the version of Python 3 you have installed.  The general workflowfor Python is to:1.  Code your lab in a text editor, e.g.code.py2.  Your code is a script.  Python will execute the script with the following command:$ python3 code.pyThis command will tell Python to execute your script line-by-line and close after exe-cution.Optional:  Hello World!Open your favorite text editor.  I prefervim:$ vim hello.pyand enter:3
print("I’m working!")Save it ashello.pyand close the editor.  If usingvim, this is:wq.  Make sure your CLI pathis in the same directory as the file you just created.  In the CLI enter:$ python3 hello.pyI’m working!Figure 2:  Hi working, I’m dad.Sidebar:  Python CLIConventionally  you  can  just  launchpython3and  type  commands  one-by-one.   But,  thealgorithms we will implement are generally large enough for this to be impractical.  Hence, Irecommend the text editor approach described above.  Suppose you want to keep the PythonCLI open after executing your script add a-iflag:$ python3 -i code.pyThis is useful if you want to check on the value of variables after a script is complete, sayfor debugging purposes.Sidebar:  Major Concepts of PythonThe following sums up the major concepts of Python:•Python is an interpretted language•Whitespace (tabs) specify blocks, not curly braces•Dynamic typing, variables do not need to be declared or assigned a type before firstuseAt the conclusion of this lab you will have a rudimentary understanding of Python whichis sufficient to complete the labs in this course.4
4    ApproachFor this lab you are given thepython3code.  We will study it line-by-line.  Copy the followingalgorithm inpython3:KB = ["looks","swims","quacks"]rules = [(["looks","swims","quacks"],"duck"),(["barks"],"dog"),(["hoots","flies"],"owl")]count = 1 # Number of times we have iterated over the whole rule setchanges = Truewhile changes:changes = False # Set the flag that there have been no changes to falseprint( "Starting iteration " + str(count) )for p in rules: # For each rule in the set of rules ...antecedent, consequent = pprint( "Consider a rule where: " )print( antecedent )print( "implies: " )print( consequent )# Determine if all literals in antecedent are also in KBanteInKB = True # Flag for the antecedent in the KBfor q in antecedent:# q will be a string# KB is a list of stringsif q not in KB:anteInKB = False # Flag as false, all clauses must be implied# If it passes the above, then antecedent is satisfied# ...and consequent should be entailedif anteInKB and consequent not in KB:KB.append( consequent )changes = Trueprint( "Antecedent is in KB, consequent is implied, KB is now: " )print(KB)elif anteInKB and consequent in KB:print( "Consequent is implied, but was already in KB")else:print( "Consequent is not implied" )5
count = count + 1print( "No more changes. KB is: " )print(KB)Do not cut and paste this into the terminal.The PDF document most likely has spacesin  it,  and  blocks  are  defined  by  tabs  in  Python.   This  may  lead  to  a  syntax  error  whencutting-and-pasting.  In the following, we discuss what happens line-by-line.  The first line:KB = ["looks","swims","quacks"]defines an array of strings.  Note that since Python is dynamically typed it infers this fromthe assignment.  We will useKBto hold the asserted atomic propositions.In lecture we mayhave called this “Facts” instead.  The next line:rules = [(["looks","swims","quacks"],"duck"),(["barks"],"dog"),(["hoots","flies"],"owl")]is an array of tuples (which you may consider an object).  Each tuple is a rule.  This tupleconsists of two parts.  The first is an array of strings, which is a list of propositional literals.By convention we assume Horn clauses.  This means that an antecedent must contain onlyliterals and we assume that they are conjuncted.  We can compactly note this by listing onlythe propositions that are in the antecedent of the rule.  For example:...["looks","swims","quacks"]...is our way of noting the antecedent should be:Looks∩Swims∩QuacksThe second part of the tuple is the consequent.  Take some time to verify that the codematches the example given earlier in the lab manual.  The next few lines:count = 1 # Number of times we have iterated over the whole rule setchanges = Truecontain some book keeping variables.countis used to keep track of the number of timeswe’ve iterated over all of the rules.changesis a flag that indicates, during an interation, ifany new knowledge was generated.  Continuing on to the first loop:while changes:changes = False # Set the flag that there have been no changes to falseThis is very important.Blocks are indicated by indentation in Python.  This is unlike Cor Java, where blocks must be specified by curly braces.  So, in Python, everything at thesame indentation level counts as being in the same block.  Note that there are no writtencommands  indicating  the  termination  of  thewhileloop.   Further  down,  there  are  some6
printstatements that have no indentation.  These statements would be considered outsideof thewhileblock.Thechangesflag is set toFalseat the start of thewhileloop.  The default state is toquit the loop.  Later on, when a consequent is asserted in the KB, we will setchangestoTrue.  Otherwise, the loop will exit at the end of the block.  Note that the literal value fortrue and false starts with a capital letter.  Continuing to the next lines of code, consider thefollowing:for p in rules: # For each rule in the set of rules ...for p in rulesis an interesting statement that is pretty unlike C or Java.  In thoselanguages,  you  would  specify  an  index  such  asiand  increment  this  number,  having  toreference the object in some way likerules[i].  However,  in Python,  this statement williterate  over  the  object  arrayrulesautomatically  for  you  without  need  for  an  arbitraryindex.  And, within each iteration of theforblock,pis the currently referenced object.  So,considering the previous code, theforloop will iterate over the contents ofrules.  The firstiterationpwill be the tuple(["looks","swims","quacks"],"duck")and so on until theend ofrules.  Now consider this statement:antecedent, consequent = pThis takes the current reference and breaks it apart.  Recall that each element ofrulesisa two-tuple.  This parts the first element of the tuple into a variable calledantecedentandthe second intoconsequent.  For example, with(["looks","swims","quacks"],"duck"),this would setantecedentto["looks","swims","quacks"]andconsequentto"duck".Recall the purpose of each iteration:  determine if a rule can be fired and, if so, fire the rule.pis a rule that is currently being considered.  To determine if it can fire, we must check theKB to see if allof the clauses are there.  Note that we’re using Horn clauses (conjunctionsof literals) in the antecedent, so if any clause isn’t there, don’t fire the rule.  That happenswith the following clause:# Determine if all literals in antecedent are also in KBanteInKB = True # Flag for the antecedent in the KBfor q in antecedent:# q will be a string# KB is a list of stringsif q not in KB:anteInKB = False # Flag as false, all clauses must be impliedThe logic is this:  initially set our belief that the antecedent is satisfied (anteInKB) totrue, then loop overKB. If any one of the clauses is not there, fail.There could probably be abreak statement somewhere in there that I didn’t include but this shouldn’t impact the resultof the code.At the end of this block, ifanteInKBremains true, it means that the rule shouldfire.  That happens with this block:7
# If it passes the above, then antecedent is satisfied# ... and consequent should be entailedif anteInKB and consequent not in KB:KB.append( consequent )changes = Trueprint( "Antecedent is in KB, consequent is implied, KB is now: " )print(KB)There are two clauses to theifstatement.  The first isanteInKBwhich is simply trueof false based on the previous chunk of code.  The second isconsequent not in KB. Thisprevents asserting a fact that is already in the KB. Otherwise, the code will loop infinitelybecause it will register old inferences as new inferences.  That is that.  You may want to alterKBto make sure the code works.5    Check offFor full credit, demonstrate your code to the instructor.