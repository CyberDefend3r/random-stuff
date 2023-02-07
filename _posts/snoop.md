---
layout: post
title: "Stop Using print() for Debugging When You Can Snoop Instead"
categories: how-to
---

# Stop Using print() for Debugging When You Can Snoop Instead

We have all used strategic `print()` statements in our code to make sure our code is running a certain line or to see the value of a variable. This gets a little sloppy and doesn't provide much context into why some code is not behaving as expected. Enter [snoop](https://github.com/alexmojaki/snoop), the super dead simple way to see exactly what the code is doing, the line of code that is executing, and when and what the values of local variables are. This can help us understand the flow of our code and identify issues much faster!

From the projects [github](https://github.com/alexmojaki/snoop):

>You're trying to figure out why your Python code isn't doing what you think it should be doing. You'd love to use a full-fledged debugger with breakpoints and watches, but you can't be bothered to set one up right now.  
>  
>You want to know which lines are running and which aren't, and what the values of the local variables are.  
>  
>Most people would use print lines, in strategic locations, some of them showing the values of variables.  
>  
>snoop lets you do the same, except instead of carefully crafting the right print lines, you just add one decorator line to the function you're interested in. You'll get a play-by-play log of your function, including which lines ran and when, and exactly when local variables were changed.

First let's install snoop:

    pip install snoop

The first way we use snoop is by adding the `@snoop` decorator to a function:
```python
import snoop

# Add the snoop decorator
@snoop
def remove_non_alpha_chars(some_string):
    """Remove all special characters from a string with the exception of spaces.
       returns a new string with special characters removed"""

    all_alpha_list = []
    
    # Loop over characters in the string
    for char in some_string:
    
        # check if char is a alphabet chsaracter or a space
        # and if so add the character to a list
        if char.isalpha() or char == " ":
            all_alpha_list.append(char)
            
    # Convert list back to a string
    alpha_only_string = "".join(all_alpha_list)
    
    return alpha_only_string

some_string = "A, Str!"

remove_non_alpha_chars(some_string)
```
This is the output to the terminal:
```bash
18:46:26.96 >>> Call to remove_non_alpha_chars in File "c:/Users/tremille/OneDrive - Capgemini/Desktop/snoop_example.py", line 7
18:46:26.96 ...... some_string = 'A, Str!'
18:46:26.96    7 | def remove_non_alpha_chars(some_string):
18:46:26.96   11 |     all_alpha_list = []
18:46:26.96   13 |     for char in some_string:
18:46:26.96 .......... char = 'A'
18:46:26.96   16 |         if char.isalpha() or char == " ":
18:46:26.96   17 |             all_alpha_list.append(char)
18:46:26.96 .................. all_alpha_list = ['A']
18:46:26.96 .................. len(all_alpha_list) = 1
18:46:26.96   13 |     for char in some_string:
18:46:26.96 .......... char = ','
18:46:26.96   16 |         if char.isalpha() or char == " ":
18:46:26.96   13 |     for char in some_string:
18:46:26.96 .......... char = ' '
18:46:26.96   16 |         if char.isalpha() or char == " ":
18:46:26.96   17 |             all_alpha_list.append(char)
18:46:26.97 .................. all_alpha_list = ['A', ' ']
18:46:26.97 .................. len(all_alpha_list) = 2
18:46:26.97   13 |     for char in some_string:
18:46:26.97 .......... char = 'S'
18:46:26.97   16 |         if char.isalpha() or char == " ":
18:46:26.97   17 |             all_alpha_list.append(char)
18:46:26.97 .................. all_alpha_list = ['A', ' ', 'S']
18:46:26.97 .................. len(all_alpha_list) = 3
18:46:26.97   13 |     for char in some_string:
18:46:26.97 .......... char = 't'
18:46:26.97   16 |         if char.isalpha() or char == " ":
18:46:26.97   17 |             all_alpha_list.append(char)
18:46:26.97 .................. all_alpha_list = ['A', ' ', 'S', 't']
18:46:26.97 .................. len(all_alpha_list) = 4
18:46:26.97   13 |     for char in some_string:
18:46:26.98 .......... char = 'r'
18:46:26.98   16 |         if char.isalpha() or char == " ":
18:46:26.98   17 |             all_alpha_list.append(char)
18:46:26.98 .................. all_alpha_list = ['A', ' ', 'S', 't', 'r']
18:46:26.98 .................. len(all_alpha_list) = 5
18:46:26.98   13 |     for char in some_string:
18:46:26.98 .......... char = '!'
18:46:26.98   16 |         if char.isalpha() or char == " ":
18:46:26.98   13 |     for char in some_string:
18:46:26.98   19 |     alpha_only_string = "".join(all_alpha_list)
18:46:26.98 .......... alpha_only_string = 'A Str'
18:46:26.98   20 |     return alpha_only_string
18:46:26.98 <<< Return value from remove_non_alpha_chars: 'A Str'
```
As you can see we get much more info than using `print()`. We get the value of the variable on each loop, the line number for each line of code, the actual code that was executed, and the value of what is returned by the function. We could of course add a few `print()` statements to get the value of the variables on each loop and what is returned by the function but that doesn't give us all the other info we get with snoop and all we had to do was add one decorator to the code.

Now you are probably like "what if I want to snoop on something that isn't a function or just certain lines in a function?". Well we just wrap the code in a `with:` block.
```python
import snoop

with snoop:
    x = 10
    y = 20
    y, x = x, y
    z = x * y
```
output:
```bash
12:53:05.55 ...... snoop = <class 'snoop.configuration.Config.__init__.<locals>.ConfiguredTracer'>
12:53:05.56    4 |     x = 10
12:53:05.56    5 |     y = 20
12:53:05.56    6 |     y, x = x, y
12:53:05.56 .......... x = 20
12:53:05.56 .......... y = 10
12:53:05.56    7 |     z = x * y
12:53:05.56 .......... z = 200
12:53:05.56 <<< Exit with block in <module>
```
Super easy, right? We get the same output as using the `@snoop` decorator on a function but just on a certain block of code.

snoop has more features like the nifty [pp()](https://github.com/alexmojaki/snoop#pp---awesome-print-debugging) method that is like using print but on steroids providing way more info than `print()` does, and a [@spy](https://github.com/alexmojaki/snoop#spy) decorator that combines snoop with the debugger birdseye.
