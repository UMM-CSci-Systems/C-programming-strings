ARCHIVED! This has been replaced with [C-strings-and-memory-management](https://github.com/UMM-CSci-Systems/C-strings-and-memory).

* [Background](#background)
 * [Testing and the Google Test framework](#testing-and-the-google-test-framework)
 * [Fixing memory problems](#fixing-memory-problems)
 * [Getting started](#getting-started)
* [The problems](#the-problems)
 * [Fixing palindromes](#fixing-palindromes)
 * [Disemvowel](#disemvowel)

---

# Background

This lab is a collection of C programming exercises with an
emphasis on strings (i.e., arrays of characters) and memory management. 
The first is an introduction to a tool for finding memory leaks, and 
the second is a simple exercise on 1-D arrays of characters

For more information (including information on how to use ```valgrind```), see
the [C programming pre-lab](https://github.com/UMM-CSci-Systems/C-programming-pre-lab).

## Testing and the Google Test framework

Each of these exercises comes with a set of tests implemented using [the
Google Test framework for C](https://github.com/google/googletest), aka 
`gtest`. You won't have to learn anything about `gtest`, but you
will need to be able to compile and run the tests that we provide.

We think the tests are pretty reasonable, but make *no* promises that
they are in any way complete. Obviously you want your code to pass at
least these tests, but you shouldn't assume that passing these tests
guarantees any kind of correctness. You're welcome to read the tests 
and extend them if you'd like. You may even need to make changes to the
test code to handle memory leaks 
[(see "fixing memory problems" below)](#fixing-memory-problems).
Do be careful to not remove or weaken the tests, though; at a minimum 
you definitely want to be able to pass the tests as given.

## Fixing memory problems

Passing the tests is arguably just the first half of each of these problems,
as it's entirely possible to pass the tests but still have substantial memory
management problems. You could, for example, have an illegal memory access
that passes the tests through happenstance, because the right value happens
to be in the necessary location. Or you could have memory leaks because you
never free up any of the memory you allocate while solving the problem in a
manner that is otherwise entirely correct. This is where `valgrind` is 
extremely helpful, as it will help identify these kinds of memory problems 
even if the tests pass. 

:bangbang: One non-obvious, but important, place to look for memory leaks is 
in the test code. If the test code calls some function `f()` that returns an
array or string that is allocated somewhere in `f` (or a function `f` calls),
then that memory is lost if the test code doesn't free up that returned array.
So if `valgrind` says there's a leak where some memory is allocated in a function and then returned to the test code, then the fix is 
_in the test code_. In general we don't encourage you to fiddle with the 
test code (you could always just change the test code to say everything 
passes!), but if the memory leaks to the test code, then that's where the 
fix has to be made.

## Getting started

You should first fork this repository to get the
starter code, and remember to add any collaborators right away. You should
then clone the repository to whatever machine you're going to work on.

There are several directories here, one for each project. 
We would recommend doing them in the order listed below; there's no 
overwhelming reason that you need to do them in any particular order, 
however, and it would be far better to move on to the
next one rather than get buried in one and not make any progress.

The basic structure for each part of the project is (for an imaginary 
part `foo`):

-   `foo.h`, which specifies the name, arguments, and return
    type of the function you're supposed to write.
    -   In every case we wrote one or more helper functions, but these
        don't have to be included in the `.h` file unless you
        want to include them in the tests.
-   `foo.c`, which includes the initial stub (or an incorrect version)
    of the program you're working with in that part.
-   `main.c`, which gives you a "main" function that you can use to
    run your code separate from the test code. You don't have to ever
    do this, but you might find it useful in debugging.
-   `foo_test.cpp`, which is the test file we wrote using `gtest`. The
    `.cpp` ending is because this is actually a C++ file not a strict
    C file. That will affect how you compile the test code, but you
    won't have to know/learn anything about C++ for this lab.

Your job then is typically to complete or fix `foo.c`, which provides
the implementation of the function listed in `foo.h`.

To compile the `main` use the following:

```bash
gcc -Wall -g -o foo foo.c main.c
```

(where you replace `foo` with the appropriate name for the project 
you're working on). If all goes well, that should generate an executable 
`foo` that you can run with `./foo`.

To compile the test code use the following:

```bash 
g++ -Wall -g -o foo_test foo.c foo_test.cpp -lgtest 
```

_Notice that this uses `g++` instead of `gcc`. This because the `gtest`
is technically a C++ library, but it also works for "plain" C code, which
is all we need it for here. The `-g` flag isn't strictly necessary; it 
causes a variety of useful debugging information to be included in 
the executable, however, which can be *extremely* helpful when using 
tools like `valgrind` or the `gdb` debugger. If you don't include it, 
for example, then those tools won't be able to report accurate or useful 
line numbers or function names. The `-lgtest` tells the compiler to include
the `gtest` library (that's the `-l` part) when generating the executable. 

---

# The problems

:bangbang: Remember: For each problem you should at a minimum

* Pass our tests, and 
* Have _no_ memory leaks, as confirmed by `valgrind`.
* Remove any print statements that you used to debug your code before you turn it in.

Also, please don't lose your brains and forget good programming practices just because you're working in a new language. C can be quite difficult to read under the best of circumstances, and using miserable names like `res`, `res2`, and `res3` doesn't help. *Use functions* to break up complicated bits of logic; it's really not fun when a group turns in a solution that is one huge function, especially when there are several instances of repeated logic.

Some things to watch our for:

* In the past there has been strong inverse correlation between length
and correctness on these problem. If you find yourself wandering off into 2
or (especially!) 3 pages of code for any of these, you've likely lost the plot
and should probably ask for some help.
* Make sure you initialize all variables (including variables used to index arrays in loops). C won't give you an error if you fail to initialize something, and sometimes you can get lucky and your tests will accidentally pass because, at least that one time, you happened to get the "right" initial value. That doesn't mean your code is correct, though.
* Make sure you allocate space for the null terminator `\0` when allocating space for strings.

There are more comprehensive tips and suggestions in `Tips_and_suggestions.md` in the repository.

## Fixing palindromes

Before you start writing your own C code, we'll start by using `valgrind`
to identify memory leaks in an existing program. In the
`palindrome` directory there is a program that
determines (in sort of a dumb way) if a string is a palindrome. The file
`palindrome.c` has the code that checks for palindromes and (instead of
doing the more obvious thing of returning a boolean) returns the string
"Yes" or "No". The file `palindrome_test.cpp` uses the Google Test 
(`gtest`) library
mentioned above to test that the `palindrome` function works. 

To run the tests you should
go into the `palindrome` directory in your project and compile the
program: 

```bash
g++ -Wall -g -o palindrome_test palindrome_test.cpp palindrome.c -lgtest
```

Run the resulting executable and
verify that all the tests pass.

If you want to run it "by hand" so you can type random things at it
then compile `main`:

```bash
g++ -Wall -g -o main main.c palindrome.c
```

You can run this with `\.main` and then type lines of text. The program will
then tell you whether each line you entered is or isn't a palindrome. You can
use `^D` to end the input, or `^C` to kill the program. You can also run it
on some "canned" input using `./main < sample_input.txt`.

Look at the code a little and see if you can spot any obvious memory
leaks. Then run `valgrind` on either your test executable

```
valgrind --leak-check=full ./palindrome_test
```

or the `main` executable, e.g.,

```
valgrind --leak-check=full ./main < sample_input.txt 
```

and see what it tells you
about memory leaks in this code. Then go through and fix the memory
leaks so that `valgrind` is happy (and the tests still pass). As mentioned
above, this might involve making changes to `palindrome.c`, or to `main.c`,
or to `palindrome_test.cpp`, or some combination of the three files.

The output of `valgrind` can be a bit overwhelming sometime, so definitely
ask questions if you're not sure how to interpret what you're seeing.

## Disemvowel

"Disemvoweling" is the act of removing all the vowels (a, e, i, o, and
u, both upper and lowercase) from a piece of text. Your task here is to
write a function
```C
char* disemvowel(char* str);
```
that takes a null-terminated string, and returns a _new_ null-terminated
string (i.e., it doesn't modify the original string) that contains the same
characters in the same order, minus all the vowels. Note that resulting
array of characters will need to be allocated, and will typically be
shorter than the input string. It would be desirable to not waste memory
and only allocate what you actually need for the return string; you
might find valgrind useful for helping check for leaks.

We've provided a `main.c` which you can compile instead of
`disemvowel_test.cpp` if you want to try out disemvoweling different
strings from the command line. :bangbang: You
need to make sure you only compile one of `main.c` and
`disemvowel_test.c`, otherwise you'll get a compiler error about trying to define `main()` twice.
