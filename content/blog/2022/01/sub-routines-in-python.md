---
title: "An Introduction to Sub Routines in Python"
date: 2022-01-24T11:20:42Z
draft: true
---

This is a lightly editted version of a document I wrote to help GCSE Computer Science students understand sub routines
(procedures and functions). It sticks quite rigourously to the [GCSE Computer Science specification from
OCR](https://www.ocr.org.uk/qualifications/gcse/computer-science-j277-from-2020/), but I thaught it might be useful for
anyone at the beginning of their programming journey.

---

As you write more complex programs, you'll find yourself repeating parts of your code over and over again. Sub routines
are one of the ways to solve this problem by defining and naming a section of code once, then simply calling upon it to
do its job when it's needed.

## Procedures

A procedure is the simplest kind of sub routine. You can define a procedure in Python like this:

```python
def procedure_name():
    # Code goes here
```

We use the `def` keyword (short for define) to start the procedure definition.
Notice how the code inside the procedure is indented, much like the code inside
an if statement, or a for loop for example.

Here's an example of a procedure that asks the user for their name, then greets them by saying hello to them:

```python
def greet():
    name = input("What's your name? ")
    print("Hello, " + name + "!")
```

To call a procedure, just write its name, followed by empty parentheses:

```python
# In the main program
greet()
```

When run, this program looks like this:

    What's your name? Michael
    Hello, Michael!

## Building a Calculator

As stated earlier, we can use sub routines to make more complex programs shorter and easier to read and write. So let's
build a calculator that asks for two numbers, then asks which arithmetic operation to perform on them, and prints the
answer.

First, let's ask for the numbers:

```python
# Ask for two numbers
num1 = int(input("Enter first number: "))
num2 = int(input("Enter second number: "))
```

Next, we need to show a menu of operations and let the user choose one.

Since showing the menu is quite a lot of code, let's make a sub routine for it:

```python
# This procedure shows a menu of arithmetic operations to perform
def show_menu():
    print("What would you like to do with these numbers?")
    print("1.  Add them")
    print("2. Subtract them")
    print("3. Multiply them")
    print("4. Divide them")
```

It's always good to explain what your procedures do in more detail using comments, as we've done here. We've also given
this procedure an appropriate name so we know what it does when we call it.

Let's call this procedure from the main program, and ask the user for their choice:

```python
# Ask for two numbers
num1 = int(input("Enter first number: "))
num2 = int(input("Enter second number: "))

show_menu()
option = int(input("Choose an option: "))
```

Now we can use selection to perform the operation that the user specified. But first, wouldn't it be nice if each
operation was its own sub routine? There's just one problem, how would we tell the rest of the program what the answer
is?

## Functions

We've talked about procedures that you can call upon to do anything you want, but what if you want to return some kind
of value to the code that calls your sub routine? Turns out, this is pretty simple, using a different type of sub routine called a function.

In Python, a function looks almost the same as a procedure [^1], but with one key difference, it returns a value, like this:

```python
def return_five():
    return 5
```

[^1]: In other languages, you may find that procedure definitions and function definitions look different from one
      another.

We can use this value by treating the function call as if it were just a value. For instance, we can assign it to a
variable:

```python
five = returns_five()
print("5 = ", five)
```

This will print "5 = 5".

We could also put the function call directly in the print statement, like this: [^2]

```python
print("5 = ", returns_five())
```

This also prints "5 = 5".

[^2]: When nesting function calls like this, be carefull that your brackets are ballanced (I.E. for each opening bracket,
      there is a matching closing bracket). This is a very common error that will cause a syntax error in your code.
  
      By the way, that's right, `print()` is actually a procedure, too. It's just one that's built into Python, so you don't
      have to define it yourself.

As you can see, this provides a way for a function to communicate with the code that called it. Let's use functions to
define sub routines for each of our arithmetic operations:

```python
def add():
    return num1 + num2

def subtract():
    return num1 - num2

def multiply():
    return num1 * num2

def divide():
    return num1 / num2
```

For each function, we give it a descriptive name, and perform the operation on `num1` and `num2`. We can access `num1`
and `num2` because they are variables in the main program, outside of any sub routine. We call variables that we can
access from anywhere like this "global variables". But we couldn't access variables in other sub routines, because they
are said to be "local variables". This means they can only be accessed from inside the sub routine they're part of.

Now all we have to do is perform the operation the user specified. Since this is also quite a large chunk of code, we'll
write a procedure for it:

```python
def process_numbers():
    if option == 1:
        answer = add()
        print("The answer is ", answer)
    elif option == 2:
        answer = subtract()
        print("The answer is ", answer)
    elif option == 3:
        answer = multiply()
        print("The answer is ", answer)
    elif option == 4:
        answer = divide()
        print("The answer is ", answer)
    else:
        print("You entered an invalid option")
```

DOn't forget to call it, this is a very common mistake to make, which leaves you wondering why none of the code you just
wrote is running:

```python
# Ask for two numbers
num1 = int(input("Enter first number: "))
num2 = int(input("Enter second number: "))

show_menu()
option = int(input("Choose an option: "))

process_numbers()
```

With that, this calculator should be fully functional! Let's have a test run:

```
Enter first number: 23
Enter second number: 14
What would you like to do with these numbers?
1.  Add them
2. Subtract them
3. Multiply them
4. Divide them
Choose an option: 3
The answer is  322
```

It works!

## Passing Parameters

The code we've written so far works fine; But what happens if we want to use the `process_numbers()` sub routine from
somewhere else in the program? Right now this would be tricky. `process_numbers()` uses the `num1`, `num2`, and
`option` global variables, so we'd have to change those variables  before we call `process_numbers()`, like this:

```python
num1 = 3
num2 = 5
option = 3 # 3 is multiply
process_numbers()
```

If we do this a lot, it requires a lot of code. It also means if we have some important value in one of the global
variables, we have to save it somewhere else before calling `process_numbers()` and in general, this is a mess. What are
we ever to do?

The solution is to specify, when defining the procedure, that it takes some parameters. Parameters are variables that
are passed to a sub routine by the code that calls that sub routine. We can use them just like local variables.

In python, to define that a sub routine takes parameters, we write their names in the parentheses, separated by commas.
Just like with variables, you can name parameters anything, within reason. We can then use them inside the sub routine.

```python
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    return a / b
```

Here we've said that each operation function takes two parameters: `a` and `b`.

If you're curious, functions like these that do not access any global variables, and compute their output based only on
their parameters, are called "pure functions". For this class of function, if you pass the same arguments multiple
times, you're guaranteed to get the same answer. Pure functions also don't have side effects (I.E. they do not change
other parts of the program, because they do not modify any global variables).

When we call this procedure, we must now provide values for these parameters. The values we pass as the parameters each
time we call a sub routine are known as "arguments". [^3]

[^3]: You'll often here the word "parameter" and the word "argument" used interchangeably by programmers, but
        technically, the *parameter* is the variable used inside the sub routine, and the *argument* is the value passed
        as that parameter *to* the sub routine.

This is great: we can now pass anything for these parameters, anywhere in the program and we don't have to change any
global variables! Let's update the `process_numbers()` procedure to pass the two numbers to these functions, and also to
take some parameters of its own:

```python
def process_numbers(a, b, choice):
    if choice == 1:
        answer = add(a, b)
        print("The answer is ", answer)
    elif choice == 2:
        answer = subtract(a, b)
        print("The answer is ", answer)
    elif choice == 3:
        answer = multiply(a, b)
        print("The answer is ", answer)
    elif choice == 4:
        answer = divide(a, b)
        print("The answer is ", answer)
    else:
        print("You entered an invalid choice")
```

We must also change where this procedure is called in the main program, to pass it some arguments:

```python
# Ask for two numbers
num1 = int(input("Enter first number: "))
num2 = int(input("Enter second number: "))

show_menu()
option = int(input("Choose an option: "))

process_numbers(num1, num2, option)
```

Here we've passed `num1` as the value for `a`, `num2` for `b`, and `option` for `choice`. This is now our entire program:

```python
# This procedure shows a menu of arithmetic operations to perform
def show_menu():
    print("What would you like to do with these numbers?")
    print("1.  Add them")
    print("2. Subtract them")
    print("3. Multiply them")
    print("4. Divide them")

def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    return a / b

def process_numbers(a, b, choice):
    if choice == 1:
        answer = add(a, b)
        print("The answer is ", answer)
    elif choice == 2:
        answer = subtract(a, b)
        print("The answer is ", answer)
    elif choice == 3:
        answer = multiply(a, b)
        print("The answer is ", answer)
    elif choice == 4:
        answer = divide(a, b)
        print("The answer is ", answer)
    else:
        print("You entered an invalid choice")

# Main program

# Ask for two numbers
num1 = int(input("Enter first number: "))
num2 = int(input("Enter second number: "))

show_menu()
option = int(input("Choose an option: "))

process_numbers(num1, num2, option)
```

