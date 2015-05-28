This tutorial teaches the fundamental concepts of [symbolic debuggers](http://en.wikipedia.org/wiki/Debugger). We will be using [Winpdb](http://winpdb.org/) to debug Python scripts. The concepts that you learn here, however, are fundamental to working with debuggers in general. At the end of the exercise, you should feel comfortable with using symbolic debuggers and have an inherent understanding of why these tools prove to reduce time and effort needed in debugging without altering the code itself to do so.



# Where to Find Other Winpdb Documentation #

The bulk of Winpdb documentation is available from the [Winpdb website](http://winpdb.org/). You can access this quickly by clicking `Online Docs` in the `Help` menu of the Winpdb window. Many of the Winpdb/rpdb2 console commands are documented within the console itself. At the console command prompt, simply enter `help`; for help on a specific command enter `help <command>`.

# Getting Started in Winpdb with simple.py #

Let's start off with a simple script to get acquainted with Winpdb.

```
#!/usr/bin/env python

"""A simple script."""

a = 1
b = 2

c = a + b

print c
```


Open the simple.py script using Winpdb with one of the two following commands:

  * For Linux/OS X users
```
winpdb simple.py
```
  * For Windows users
```
%PYTHONHOME%\Scripts\winpdb simple.py
```

The **Winpdb window** will open and begin "attaching" to the script, hereon called the **debuggee**. You will also see a blank **output terminal** open in a separate window. You should see something like the following:

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_window.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_window.png)

Let's briefly examine what happened after Winpdb started. The debugger, Winpdb, launched the script, but rather than letting the script run to completion, it halted execution of the script before the very first line of code could be executed. In effect, Winpdb placed simple.py in suspended animation, awaiting your command to allow it to proceed. We will see how to make the script begin executing in a moment. Right now, let's get oriented with the Winpdb window.

# The Winpdb Window #

The Winpdb window has several parts, some of which allow you to interact with the debuggee, some of which convey information about the **debuggee** or the state of the debugger.

## Source Browser ##

Perhaps the most intuitive frame of the Winpdb window, the source browser appears in the top right of the window.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_source_frame.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_source_frame.png)

This frame displays the source of the script or module currently being debugged. Later we will see that the source browser dynamically updates to display code called from external modules. We will see how to interact with the debugger using the source browser, however most of its utility comes simply from being able to see the source as the debugger executes it.

We can see that the browser provides syntax highlighting for readability. It also provides the line numbers in the left column of the frame. The browser highlights the current line in blue. It is very important to note that the current line is **the next line to be executed; it has not yet to been executed**.

A special status indicator character appears between the line number and the code of the current line. Right now, we see the character is `C`. We will discuss the specific meanings of the status character later.

We will also see later how the source browser displays and allows us to set and remove breakpoints.

## Console ##

The console sits just below the source browser.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_console_frame.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_console_frame.png)

The console allows us to interact with the debugger directly and provides a great deal of power. We will see how to use the console later.

## Menu and Controls ##

The Winpdb menu and controls sit at the top of the window.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_controls.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_controls.png)

Feel free to explore the menu options on your own. We'll focus on the controls more in a bit.

## Debuggee State ##

These frames, which monitor the state of debuggee, appear in the left side of the window.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_state_frame.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_state_frame.png)

The **Namespace** frame keeps track of variables and their values in realtime as the debugger executes the debuggee code. It tracks debuggee data among three levels: **Locals**, data within the current scope; **Globals**, data within the global scope; and **Exception**, for examining exception data.

The **Threads** frame provides information on threads of the debuggee. Discussion of threads falls beyond the scope of this exercise.

The **Stack** window frame displays the call stack, with the top frame of the stack being the most recently called. `rpdb2.py` will always occupy the lowest levels of the call stack as this is Winpdb's backend for debugging.

**NOTE**: Here we must make a distinction for clarity: stack frame will refer to the frame within the call stack, not to be confused with the Stack window frame within the Winpdb window. Where possible, I will capitalize "Stack" when referring to the window frame, and use an uncapitalized "stack" when referring to a frame within the actual call stack.

## Control Basics ##

Now that we're oriented with the Winpdb window, let's start learning how to use the debugger. We will do this using the debugger controls. Let's take a closer look at the control panel, located in the Menu and Control frame. We'll focus on the left half of the control panel and leave the right half buttons for exploring on your own.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_ctrls_expl.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_ctrls_expl.png)

### Go ###

The **`Go` button**, which appears on the left side of the control panel, allows the debuggee to run to whichever comes first: a breakpoint (explained later), an exception, or the end of the program. Let's see Winpdb run `simple.py`. With no breakpoints or exceptions encountered, the debuggee will run as if it were simply being executed straight from the Python interpreter.

Click the Go button. The debugger will move through each line and back down through the call stack. The source browser will show the current line as `rpdb2.setbreak()`. The Stack window frame will show only one frame in the stack, frame 0, that pertains to `rpdb2.py`. If you look to the terminal window opened by Winpdb, you should see 3, which pertains to the `print c` statement in `simple.py`.

At this point, the debugger has stopped running our script. That's a pretty short ride, and we didn't really get a feel for what's going on. We'll spend the rest of the exercise learning how to better control the execution of our debuggee.

#### Restarting the Debugging Session ####

Now that `Go` zipped right through every line in our script, we need to reset the debugger. We can do this in one of two ways:

  1. The Lazy Way
> > Close the Winpdb window (it should close its terminal along with it), and repeat our `winpdb` command.
  1. The Smart Way
> > Under the `File` menu option, you will find a **`Restart`** command. Click this and Winpdb will restart the debugging session for you.

### `Next` ###

The **`Next` button**, which sits in the middle section of the control panel, executes the current line and proceeds directly to the next statement in the current scope.

As we mentioned earlier, the browser window has a status indicator character, currently at `C`. The **`C`** indicates the debugger just entered the code at the current scope. As of yet, the debugger is not actually ready to execute the current line. Let's make it ready.

Click `Next`. You should see the status character change from `C` to **`L`**, where `L` indicates the debugger is prepared to execute the current line.

Click `Next` again. You should see the debugger move to the next line, the expression `a = 1`. Click `Next` again and the debugger will execute this statement.

#### Explore the (Name)space ####

The debugger now tracks the variable `a` and its value.

Let's explore the **`Namespace`** frame [[1](DebuggingTutorial#Footnotes_of_little_note.md)]. You should see `a` at the bottom of the Locals tab (you may need to scroll down) and it should have the type `int` and value of `1`.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_a.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_a.png)

Click `Next` again to execute the statement b = 2 and you should now see that the Namespace frame now contains `b` as well. If you click the Globals tab, you will see that both `a` and `b` are there as well. Why?

Click `Next` again and you should see `c` now appears in the Namespace tabs. What is its value?

#### Scope Completion ####

By this point, `print c` should be the current line. Click `Next`. Notice the status character changes from `L` to **`R`**, which indicates the current scope is complete and is prepared to return. "Return" in this context refers to the way functions return values when they have completed. We will see a better example of this later. For now click `Next` again to allow the return.

You will now see the debugger has started moving back through the stack. `rpdb2.py` will be the file of the current stack frame, and the calling function is "StartServer". You will also see the source browser has updated to show the code of the new current line, which is now in the `rpdb2.py` file. The status character is R again, indicating that this scope is also prepared to return.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_up_stack.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_up_stack.png)

Click `Next` until you reach the line with `rpdb2.setbreak()`. Does this line look familiar? It should--this is the very same line the debugger ended on when we clicked the Go button several minutes ago. We have just stepwise told the debugger to execute every line of code in `simple.py`.

Often times we will only want to execute a small portion of the debuggee script or program piecewise; the rest we wish to run as normal. In the next section, we'll look at just how to do this using breakpoints.

## Breakpoint Basics ##

A **breakpoint** is an instruction that tells the debugger to pause execution once it reaches that line. Let's set our first breakpoint. We would like to examine values of both `a` and `b` to determine what the resulting value of `c` might be. Therefore, let's set a breakpoint for the line with the statement `c = a + b`, which means `c` is about to be, but is not yet, defined.

If you have not yet done so, restart the session. In the source browser window, position your mouse between the line number and the statement `c = a + b`, and left-click. The line should turn red, indicating we have set a breakpoint at this line.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_bp.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_bp.png)

With our breakpoint in place, we can run the code straight to this point, without having to click `Next` repeatedly. Click the `Go` button. You should see the code execute and then halt at the line with the breakpoint. At this point you can inspect the values of `a` and `b`, make a guess as to what `c` will be, and click `Next` to execute the statement. After you're satisfied, click `Go` to finish execution of the debuggee.

To unset a breakpoint, simply click on the line the same way you did to set the breakpoint. You will see the line return from being highlighted in red to normal white. For most breakpoints, that's all there is! Pretty simple, isn't it? You can set as many or as few as you would like. Later we will see how to create more sophisticated breakpoints.

## Console Basics ##

The console provides a power tool for interacting with the debugger. In fact it has all of the commands available from the control panel and many more not available via either the control panel or the menus. Let's start with a simple example: since we need to restart our debugging session, click inside the console's command area type in **`restart`** and push Enter. This will restart the debugging session, the same as if you had clicked `Restart` under the `File` menu.

Likewise, we can execute the current line of code by typing in **`next`**. Give it a try—enter in `next` to enter in the current scope, and then `next` again to execute the first statement. Most frequently used console commands come with shortcuts. The shortcut for the next command is **`n`**. Enter `n` to execute the second statement.

You should now be at the statement `c = a + b`. Let's take a look at a very powerful command of the console: **`exec`**. The shortcut for exec is **`x`**. This command takes a single statement as an argument and executes that statement within the context of the current scope. (We will discuss more about scope later.)

Currently, `a` is set to `1`. (Where should you look to confirm this?) Let's instead change its value to `3`. To do this, enter the following in the Command box:

```
exec a = 3
```

This command will set the value of `a` to `3`. Confirm this.

You should currently be at the statement `c = a + b`. Click `Next` or enter `next` at the console to execute this statement. Is the value of `c` the same as earlier or different? Why or why not?

`exec` even allows us to create brand new variables in the current scope. Try the following command:

```
x d = 4
```

Did this create a new variable `d` and give it the value `4`? How can you check?

Let's look at another powerful command, **`eval`**. The shortcut for this command is **`v`**. Slightly different than `exec`, `eval` takes a single expression as its argument and returns the results of that expression to the console. Try the following command:

```
eval a + b
```

What number is returned?

We can even evaluate with our session-created variable `d`. What does the following return?

```
v b + d
```

Unlike `exec`, `eval` does not affect the state of the debuggee, it merely evaluates the expression in the context of the debuggee's current scope. For instance, try the following command:

```
v a = 1
```

What happens? Is the value of a now `1`? Why or why not? (Hint: Think about the difference between a statement and an expression.)

Let's learn one more console command and then call it quits with `simple.py`. The jump command allows you to jump forwards or backwards to any statement in the current scope. The shortcut for the jump command is **`j`**. The jump command takes a single argument, an integer representing the line number to jump to.

We would like to reset a back to its original value. We can do so by jumping back to the statement `a = 1`. Enter the following at the console:

```
jump 5
```

You should see the current line "jump" back up to line 5, and the status character should indicate that the debugger is prepared to run the statement. Run it with the `Next` button or `next` in the console. Does this change the value of `a`?

We would now like to reset the value of `c`, which depends on `a`, back to what it should have been. Since we have not affected the value of `b`, we can choose to jump over that line to the line of the statement we wish to re-run, `c = a + b`.

```
jump 8
```

Now execute this line. What is the value of `c` now?

## simple.py Wrap Up ##

This concludes working with `simple.py`. At this point, you should be familiar with the following:

  * What each frame of the Winpdb window displays and/or allows you to control
  * How to run a whole script and how to run a single line
  * How to restart a debugging session
  * How to set a simple breakpoint
  * How to manipulate variables and evaluate expressions within the current scope

# Diving Deeper with divisibles.py #

We now progress to a more complex scenario: a script with a function and a loop to allow us to illustrate some more beautiful properties of symbolic debuggers. If you haven't done so already, close the Winpdb window.

Our debuggee now becomes the script `divisibles.py`.


```
#!/usr/bin/env python

"""
Takes a positive integer as an argument and prints all of its divisors.

example usage:
    python divisbles.py 100

""" 

import sys


def is_divisible(a, b):
    """Determines if integer a is divisible by integer b."""
    
    remainder = a % b
    # if there's no remainder, then a is divisible by b
    if not remainder:
        return True
    else:
        return False


def find_divisors(integer):
    """Find all divisors of an integer and return them as a list."""

    divisors = []
    # we know that an integer divides itself
    divisors.append(integer)
    # we also know that the biggest divisor other than the integer itself
    # must be at most half the value of the integer (think about it)
    divisor = integer / 2

    while divisor >= 0:
        if is_divisible(integer, divisor):
            divisors.append(divisor)
        divisor =- 1

    return divisors


if __name__ == '__main__':
    # do some checking of the user's input
    try:
        if len(sys.argv) == 2:
            # the following statement will raise a ValueError for
            # non-integer arguments
            test_integer = int(sys.argv[1])
            # check to make sure the integer was positive
            if test_integer <= 0:
                raise ValueError("integer must be positive")
        elif len(sys.argv) == 1:
            # print the usage if there are no arguments and exit
            print __doc__
            sys.exit(0)
        else:
            # alert the user they did not provide the correct number of
            # arguments
            raise ValueError("too many arguments")
    # catch the errors raised if sys.argv[1] is not a positive integer
    except ValueError, e:
        # alert the user to provide a valid argument
        print "Error: please provide one positive integer as an argument."
        sys.exit(2)

    divisors = find_divisors(test_integer)
    # print the results
    print "The divisors of %d are:" % test_integer
    for divisor in divisors:
        print divisor
```

This script takes a positive integer as an argument and prints out all integers that divide that integer. (An integer `m` is said to be divisible by an integer n if the remainder `r` of `m/n` is 0.) Let's try it out.

```
python divisibles.py 100
```

What resulted? Does this seem like a reasonable result (i.e., are these all the numbers that divide 100)?

If you answered "Yes," please do yourself a favor by shutting down your computer and taking a nap—you're in no condition to debug

If you answered "No," what should your next thought be? Your choices are:

  1. "I should try more numbers as test cases."
  1. "I should look at the source code."
  1. "I should open this up in a debugger and find out what's going on."
  1. "That's nice, but how are we going to inflate the elephant with the foot pump?"

Here are the points for your answer:

  1. 30 points: A good idea, more cases never hurt
  1. 50,250 points: A good idea considering that up to 90% of known bugs can be discovered by rigorous code inspection [[2](DebuggingTutorial#Footnotes_of_little_note.md)]. For the sake of instruction, let's assume you've already inspected your code and still can't find the mistake.
  1. 1,380,296 points: You're on the right track.
  1. -6.0221415 × 10<sup>23</sup> points: What the heck? No more coffee for you, code monkey!

Okay, so let's debug this script. You know the drill:

  * For Linux/OS X users
```
winpdb divisibles.py 100
```
  * For Windows users
```
%PYTHONHOME%\Scripts\winpdb divisibles.py 100
```

If you look in the source browser, you'll notice this piece of code is more substantial than the last. Nonetheless, we can navigate through it the same. Click `Next` to enter the code. After the `import sys` statement, there's a function definition for `is_divisible`. Click `Next`. Notice how the debugger proceeds "over" the code of `is_divisible`? This is because the code is not executed until `is_divisible` is called. Click `Next` to proceed over the `find_divisors` function definition.

You should now be at the top of an `if __name__ == '__main__':` statement that checks to see if the script has been called directly.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_ifmain.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_ifmain.png)

Click `Next` to proceed into the `try` statement and again to proceed into the `if len(sys.argv) == 2:` statement. Run the statements in there, confirming that `test_integer` is set to `100`.

Note that if the debugger skips over these statements, and instead skips directly to line 52, it is likely that you forgot to pass an argument to `divisibles.py` when you invoked the debugger!

Click `Next` at the `assert` statement. Notice how the debugger proceeds past the rest of the logic statements. On your own time, you can try passing various values as arguments to `divisibles.py` to explore these other logic paths. For now, let's focus on the problem at hand: why are we not getting all the divisors of our input integer?

You should now be at the statement `divisors = find_divisors(test_integer)`.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_fd_call.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_fd_call.png)

Click `Next` one more time to execute this statement, which calls the `find_divisors` function. We now need to inspect the result from the function call, which is stored in the `divisors` value.

## Filtered or Unfiltered? ##

At this point, you might notice that our Namespace frame is getting crowded. The **`Filter` button**, third from the right, controls which variables will appear in the Namespace frame. By default, it is set to Medium. You can click the Filter button to move it to Maximum, and again to turn off the filter completely. Which entries are filtered out, and which aren't? Turn the filter back on to Maximum.

Now that we've cleaned up the Namespace a bit let's inspect our result, `divisors`. Notice that Winpdb represents that it is a list under the Repr column with the brackets, just as if we would see had we printed `divisors`. Only, instead of printing, we can see the value in the context of the rest of the program and other values—as the program executes! Goodbye print statements; hello debuggers!

You may have noticed an arrow next to some of the variable names in the Namespace. These are **expansion tabs**. Click the expansion tab for `divisors`.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_filtered.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_filtered.png)

Notice how this now gives detailed information on every element contained within the list. The `divisors` list is rather small, now, so we can already get most of this information without having to expand it, but for large structures, this can be very handy. Click the expansion tab again to re-collapse the displayed information.

Well, we've found that the result for our `find_divisors` call gave a disappointing whole two results. So we can restrict the area of our problematic bug to likely lying in the code within that function, or to calls being made from that function. Notice, though, that although the function was called and the return value collected when we clicked `Next`, we didn't actually get to see the code in the function execute. We know the bug lies somewhere in there, so how do we get to it? Enter the `Step` command.

## Stepping in for a Bit ##

Since we executed our script past the point we want to inspect it, let's restart our debugging session so we have a pristine state. Do so either through the menu or the console.

At this point, depending on how much coffee you have consumed by this point, you may be asking, "What happened to the argument of 100 we passed to the script when we launched the debugger? Is it still there or did it disappear?" To answer that question, execute past the `import sys` statement so that the `sys` module is loaded, and then evaluate sys.argv. What is the result? (Hint: you should be looking for a value in `sys.argv[1]`.) Though you will find using `eval` quicker, you could also, alternatively, turn off the `Filter` button and expand the tabs of `sys` and `argv` to inspect the values.

Do you feel confident that our argument value was retained? Good. Let's proceed quickly to the part of the program we're most interested in. Think about what tool we learned about working with `simple.py` that would allow us to execute lots of code but stop when we reached an important line. Got it yet? If not, stop, really think, or at least guess...

Okay, got a guess?

If you said "We should set a breakpoint!" you hit the nail on the head. Set one at the call to `find_divisors`. Now get to this line either by clicking the `Go` button, or by entering **`go`** (shortcut **`g`**) in the console.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_fd_bp.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_fd_bp.png)

Now, we need a way to get inside the call to `find_divisors`. The `Step` command allows us to perform such a feat. The **`Step` button**, or alternatively, the **`step` command** (shortcut **`s`**) allows us to step into the code in a called scope (e.g., a function or a method) being called and run through that scope's lines of code. This even works for calls made to code in other modules.

Click `Step` or enter `step`, now, to enter the `find_divisors` function call. This should transport you back to the function's declaration statement. Notice the status character this time, however, is `C`. Remember what this means? We're about to enter a new scope, the scope of the `find_divisors` function. Also take note of the Namespace frame. Take a look at the Locals, then compare this to the Globals. Are they different now? What appears in Local now that didn't before? What doesn't? How is this different from before? Why is it different?

Click `Next` or `Step` to complete entering the new scope and proceed to the first statement.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_scope.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_scope.png)

Click `Next` or `Step` again to run the first statement of `find_divisors`. Did you notice a new variable enter the Locals tab of the Namespace frame? Did this change also appear in the Globals tab? Why or why not? Run through the next two statements and observe the changes in the Namespace.

You should now be at the while loop of the `find_divisors` function. Click `Next` or `Step` to go into the loop. Note that both `Next` and `Step` proceed into a loop, but only `Step` proceeds into a new scope (e.g., a called function). You should now be at the following statement:

```
if is_divisible(integer, divisor):
```

Notice there is a call to the `is_divisible` function with the arguments `integer` and `divisor`, and that its result is being evaluated by the `if` statement. Let's go into this function. Make note of the values for `integer` and `divisor`, then click `Step`. You should be transported to the declaration of the `is_divisible` function, and the status should be `C`.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_is_div.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_is_div.png)

Notice the values for the two variables denoted as arguments, `a` and `b` are already defined, even though we have not yet completed stepping into the new scope. Compare these values to the values of `integer` and `divisor` that you passed to the function.

Go ahead and proceed to execute past the `remainder = a % b` statement. What is the value of remainder? Make a guess as to which of the two logic forks the program will run through based on this answer, then confirm by executing the statements.

When you reach the `return` statement, you will get an `R`. Do you remember what this means? Proceed to return back to the calling scope. Notice that the variables tracked in Locals updates back to the previous scope. You should now be at the following statement under the if statement that we called `is_divisible` from:

```
divisors.append(divisor)
```

What does reaching this statement indicate? (Hint: Think about the name of the called function, `is_divisible`.) Proceed to execute this statement. Did you notice a value get updated in the Namespace?

You should now be at the following statement:

```
divisor =- 1
```

What do you anticipate this statement does? Go ahead and run it.

You should now be back at the top of the while loop. But wait! Look very closely in the Namespace. Do you notice anything suspicious?

You now realize at what line the bug is occurring. In fact, you may have discovered the actual cause of the bug on that line.

Still don't see it? Take another look. Think about that unexpected change that happened to divisor...

Got it?

Clearly, the author of the script intended to decrement the value of divisor by 1. However, that was not the actual result of the statement.

In Python, the whitespace between operators is often optional. This means that

this

```
divisor =- 1
```

and this

```
divisor=-1
```

and this (starting to get it?)

```
divisor =-1
```

and this (got it now?)

```
divisor = -1
```

are all equivalent, because the operator is actually the equals (`=`) sign! So the result is that, rather than subracting 1 from divisor, the statement is actually assigning divisor the value of `-1`! [Inconceiveable](http://www.youtube.com/watch?v=D58LpHBnvsI)! Yet, true!

The proper order of the symbols should have been `-` first, followed by `=`. Thus the author intended to use the operator `-=` operator, but the actual code simply only uses a `=` operator. D'oh!

Go ahead and open `divisibles.py` in your preferred text editor or IDE and change the line from

```
divisor =- 1
```

to

```
divisor -= 1
```

Alright! Congratulations bug hunter!

But, before we get carried away with our elite bug hunting skills, let's make sure this fixed the script.

We don't have to close Winpdb to try out our edited script, we can simply `restart` the debugging session and Winpdb will take care of re-executing as it was run previously, but with the updated code. It will even retain any previous breakpoints we set or arguments given on the command line! How handy!

Restart the debugging session, confirm you still have a breakpoint on the line calling `find_divisors` and click Go to run to that point. Click `Step` to proceed into the `find_divisors` scope. Use `Step` to proceed sequentially into the while loop and into the `is_divisible` via the `if is_divisible(integer, divisor):` statement.

Step through to make sure everything looks in order. It should return True since 50 divides 100. Return to the scope of `find_divisors` and `Step` until you reach our altered statement of `divisor -= 1`. `Step` to execute this statement. Did it decrement correctly?

Now `Step` through another iteration of the while loop. `Step` into the next call of `is_divisible`. We know that 100 is not divisible by 49, so let's go through the logic paths and make sure that is\_divisible returns False. `Step` back to the scope of `find_divisors`, confirm 49 isn't appended, and that divisor is properly decremented. `Step` back into the next call of `is_divisible`, which should be called with the values 100 and 48 now.

## Return to Caller ##

You should be at the declaration of `is_divisible` with the status of `C`. By now this has already gotten old. We probably should have used `Next` instead of `Step` so we could at least let the code in `is_divisible` run without supervision, since we're now confident its code is correct. Since we're inside the function, though, we now need to get back out of the scope of `is_divisible`. You could click `Step` or `Next` a couple of times to get there, but what if the function was really long? That would be unfeasible. There's got to be a better way.

Indeed, a better way exists. The **`Return` button**, or its equivalent console command return (shortcut **`r`**) will get you out of a scope in a hurry by executing that scope's code and taking you directly to the return to the calling scope. Go ahead and click `Return` or enter `return`.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_return.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_return.png)

You should be taken to the statement `return False` with the status `R`. Click `Next` and you will be returned to the calling scope of `find_divisors`.

Click `Next` to go through a few more rounds of the while loop without going through the is\_divisible code. When you are satisfied that the code is working properly, you can just let the debugger handle the rest of the execution. Clicking `Go` will do this, and should allow our debuggee to proceed to completion and exit. Click `Go`.

Oh no! What happened?!? Rather than the code executing cleanly, an exception was raised, which terminated execution!

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_exception.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_exception.png)

Winpdb graciously prompts us to let us know that it caught an unhandled exception raised by the debuggee and asks if we would like to analyze it. Go ahead and click `Yes`, stifling the urge to smash the mouse button into oblivion as you do so, and take a deep breath as we dive into this previously masked bug.

## Exceptional Analyses ##

Clicking `Yes` in the dialog sends Winpdb into Exception Analysis mode, as evidenced in two ways: the **Analyze Exception button**, found at the right side of the control panel, now appears activated, and `"Analyze mode was set to ON."` appears in the console. Exception Analysis mode takes us to the state of the program immediately when the exception was raised and left uncaught.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_zero_division.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_zero_division.png)

We see now that some line in our code raised a `ZeroDivisionError`, which meant either the statement attempted to divide by zero, or it attempted to do a modulo operation with zero. Now that we know the exception type, let's go back to analyzing the state of divisibles.py when it raised the `ZeroDivisionError`. We can use this Stack data to help us trace down what caused the raising of our exception.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_exception_stack.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_exception_stack.png)

Winpdb orders the calls in the stack from top to bottom, with the most recent call's stack at the top, and progressively less recent calls lower than that. However, in terms of stack frame number, the most recent call has the lowest number (0), with progressively less recent calls having incrementally higher frame numbers.

Notice also that the filename a stack frame belongs to is also listed with the frame. This is convenient because automatically we know we only have to look through at most three stack frames to decide what events may have lead to the `ZeroDivisionError` (since only the top three frames belong to `divisibles.py`).

If we click the top stack frame, we see that the offending line is in the `is_divisible` function at the modulo operation to calculate remainder.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_frame_0.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_frame_0.png)

The `E` next to this statement further indicates this is the point where the `ZeroDivisionError` was raised. So we now know the "What" and "Where" of the story, but we still need to try and deduce the "Why". We can see in the Locals tab in the Namespace frame that `b`'s value in this frame is `0`, and using `0` for a modulo operation obviously raised the error. But how did `b` reach `0` in the first place? Remember that b is given assigned as an argument during the call of `is_divisible`. This means this `0` had to originate at a less recent frame of the call stack.

Let's take a look at frame 1, which is `find_divisors`. Click this respective line in the Stack window frame to bring up this call stack and allow us to examine its state just prior to the crash.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_frame_1.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_frame_1.png)

We can see the statement that calls `is_divisible` from within `find_divisors` and its passing of the arguments `integer` and `divisor`. We can also see from the Locals tab in Namespace that divisor was set to `0` when passed to `is_divisible`. This is odd, because we have already fixed the bug that "decremented" divisor past `0` when we fixed the reversed symbols. But we know that the bug must be somewhere contained within `find_divisors` since the variable divisor is local to this scope...

Maybe this was just a fluke. Let's see if we can repeat the error. Maybe by doing so we can find the incriminating underlying bug.

## Breaking Under the Conditions ##

We're starting to lose patience after chasing this bug around. We need a way to get to this bug in a hurry. That means we need a breakpoint, but we suspect our bug is hidden beneath a loop, which means any breakpoint in that loop will stop execution upon every iteration. What we really need is a breakpoint that only breaks execution when we want it to. Enter the conditional breakpoint.

A **conditional breakpoint** remains inactive (that is, code is allowed to continue past it) until some specified condition is reached, at which point, the breakpoint becomes active and halts execution at the line which the breakpoint is set. Because specifying a condition entails specifying an expression, we can only set these advanced breakpoints from the console. The console command bp allows us to set breakpoints; to set a conditional breakpoint, at the console, enter

```
bp <line number>, <expression>
```

substituting `<line number>` and `<expression>` for an actual line number and Python expression.

In our case, we want to see what happens in `find_divisors` when `divisor` reaches `1`. Does it still get further decremented to `0`? Let's set a conditional breakpoint to halt the while loop when divisor reaches `1` so we can manually move through the code at that point.

If you haven't done so already, `restart` the debugging session. Next, we need to set our conditional breakpoint. Look for the line number of the while loop statement:

```
while divisor >= 0:
```

and set a conditional breakpoint for when divisor reaches the value `1`.

```
bp 35, divisor == 1
```

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_cond_bp.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_cond_bp.png)

While we're at it, let's unset our other breakpoint that we set many restarts ago. While we can do this by scrolling down and clicking the line to unset it, we can also clear the breakpoint from the Console. First, we obtain the ID of the breakpoint using the command **`bl`** (that's a lower-case 'L', not the digit one), which lists breakpoints and their information on the console.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_bl.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_bl.png)

We can see that our first breakpoint, set on line 70, has an ID of `0`. We can unset, or clear, this breakpoint using the **`bc`** command, which takes an argument of an ID of the breakpoint to be cleared. We clear our first breakpoint with the following command:

```
bc 0
```

Now that we have our breakpoints properly set and unset, let's run the program. Click the `Go` button.

You should now be on the if statement immediately within the while loop, and divisor should have a value of `1`.

![http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_divisor.png](http://wiki.winpdb.googlecode.com/hg/images/tutorial/winpdb_divisor.png)

Let's `Step` through to make sure everything is running as expected at this point. We see that `is_divisible` returns `True` as it should, since 100 is divisible by 1. Continuing, we see that divisor is decremented by 1 and returns to the top of the while loop, which should bring the while loop to an end... but wait!

Click `Step` to run the evaluation of the while loop condition and we see that the body of the loop is once again executed! It was supposed to stop once divisor reached 0, but instead it's continuing...

Why did `divisor` at value `0` pass the evaluation?

Wait...

Do you see it, too?...

Look very closely at the evaluation statement `while divisor >= 0:`...

We thought that the evaluation would be `False` once `divisor` reached `0`, because we know that `1` will always be the last integer that divides a positive integer. After all, we can't divide an integer by 0 [[4](DebuggingTutorial#Footnotes_of_little_note.md)]. However, what we thought we saw and what was _actually_ there were quite different.

In reality, the evaluation will be true for any number greater than, _or equal to_, zero. [[4](DebuggingTutorial#Footnotes_of_little_note.md)]

At this point, we slap our collective forehead, and open the script back up in our editor or IDE, and change the statement from

```
while divisor >= 0
```

to

```
while divisor > 0
```

It's important to note that this demonstrates a very important phenomenon in software development: bugs tend to cluster [[2](DebuggingTutorial#Footnotes_of_little_note.md)]. However at this point, we can surmise we have found all the bugs. At the very least, if `divisibles.py` doesn't work by now, we need to shutdown our computer and do something else, because we've run out of brain juice. Just for fun, let's check and see if we did indeed locate and remove all the bugs from the script.

Restart the debugging session one last time. Let's go ahead and clear all breakpoints. We can do this either by going to the menu, and selecting `Breakpoints`, then `Clear All`, or by going to the Console and using the `bc` command:

```
bc *
```

Now that we've removed all breakpoints, click `Go` to let the program run. We should now see the program successfully run through to completion. If we look to the output terminal, we should see the following:

```
The divisors of 100 are:
100
50
25
20
10
5
4
2
1
```

We sigh in relief, pat ourselves on the back, and call it a day... or an evening... or a very late night...

## divisibles.py Wrap Up ##

After working your way through `divisibles.py`, you should feel familiar with:

  * Debugging code that actually has bugs
  * Filtering the Namespace
  * Stepping into and Returning out of a called scope
  * Analyzing exceptions
  * Reading and navigating the Stack
  * Setting and clearing breakpoints from the console
  * Setting conditional breakpoints

# Final Words #

At this point, if you have successfully completed the tutorial, you have leveraged every major tool that a symbolic debugger can provide. Hopefully you found learning through Winpdb easy and worthwhile. Take the concepts here and apply them to any other language you write in that offers debugger support.

Happy hacking!

# Footnotes of little note #

  1. By the way, if you missed the reference of the title, try [a search for "More Cowbell"](http://video.google.com/videosearch?q=more+cowbell)...
  1. Glass, R. 2000. _Facts and Fallacies of Software Engineering_. Addison-Wesley. pp. 104-105.
  1. Well, we can't divide by zero, but [Chuck Norris](http://uncyclopedia.org/wiki/Chuck_Norris/Facts#Math_.26_Science) can.
  1. By the way, for those of you who figured this out an hour ago, thanks for keeping your trap shut and just going along for the ride to learn for the sake of learning.