### Intro

[![watch on youtube]()]()

Hi everyone welcome back to another julia language tutorial. We continue to work on our image puzzle game
We have ended the last tutorial with creating a function that generates our opening view of the game
 Today we will see how to “start” the game more easily by saving all the code to a file.
We will see the differences between running Julia scripts or from the julia session


### Script:
I have copied the function we created last time into a file PuzzleGame.jl
We need some “top-level” code to actually start the run
```julia
using Images, ImageView, TestImages
using Random
function init_game()...

# top-level code
Img = testimage("fabio")
New  = init_game(img)
imshow(new)
sleep(3)
```

To run the file we just type: `$ julia PuzzleGame.jl` in the command line

You can think of it as if you open a new julia process and write all the statements from the file - this is why we need that “top-level” code.
after julia finish running all statements the process ends and the image window closes because it’s father process ends.
This is really Slow… If you remember the JIT compilation you will understand why it’s so slow, Julia actually compiles everything it read in the file (including the package we use)
You really should not use scripts,   unless you have a good reason - maybe running

Most of the time a module will be the better way to work, and once you have opened a julia REPL and loaded the module not extra compilation time for multiple runs

### Modules
Are actually encapsulated code keeping it’s inner namespace,
They help us create logical  blocks of code sharing outside, only what is relevant for the user to use (using export == public)
This enables us abstraction and re-use of functionality
Using Vs import - using bring to scope every thing defined as export and import specifies exactly what we want to bring to the current name space
Julia style uses camel case for module names
Standard modules:
+ Core - the core language is under core module
+ Base - is common used methods
+ Main - The main process running: This is actually the main REPL

To create a module we use the module keyword
don’t leave top-level code ⇒ it will run when you load the module with using - we can use isinteracitve() to distinguish between running as a module (inter-active) or as a script


```julia
module PuzzleGame
using Images, ImageView, TestImages
using Random

export main

function init_game()..
end

function main()
end

if !isinteractive()
	main()
  sleep(3)
end

end # module PuzzleGame
```

### Revise
Before we will load the new file to our REPL. make sure you open a fresh REPL so the workspace is clean. And we will add a new package- Revise
This package reduces the need of restarting Julia session which consumes a lot of time
It tracks modules and files - for example it replaces older versions cleanly
This improves the developing process when you add more changes to the module
Let’s load the model:
```julia
includet ("./PuzzleGame.jl")
using .PuzzleGame  # using Main.PuzzleGame
```

> using .PuzzleGame is a short version of Main.PuzzleGame, stating that the module in part of the Main (not parallel as loading packages
  )

Let’s see some of the concepts we talked about
NameSpace + autoLoading:
we can add a new function to the module
```julia
function rules() = println("swap tiles to solve...")
```
To access the new function we need to use the NameSpace directly: `PuzzleGame.rules()`


So, we can start our game with our main function. And that’s our first step in really getting an interactive game
We covered for how to use modules any why are they when they are better than scripts.
Next we will cover the GUI - and see how to get information out of the imshow window.


---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  yayo.prg@gmail.com
