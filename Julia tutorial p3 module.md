### Intro

[![Julia tutorial p3](https://yt-embed.herokuapp.com/embed?v=l5YgOb1YPII)](https://youtu.be/l5YgOb1YPII "Julia tutorial p3")

Hi everyone welcome back to another julia language tutorial. We continue to work on our image puzzle game
We have ended the last tutorial with creating a function that generates our opening view of the game
Today we will see how to  activate (“start”) the game.
To do so, we will create a file containing all the code for the game. And will cover some of the different ways to run it.


### Script:
A script is a file we can run directly from our command line.
I have copied the function we created last time into a file PuzzleGame.jl
We need some “top-level” code (meaning, runnable, it is not under and functinon definition) to actually start the run
```julia
using Images, ImageView, TestImages
using Random

function init_game(img::Array{T,2}; nof_row_blocks::Int=4, nof_col_blocks::Int=4 ) where T  <: Colorant
    block_size = Int.(size(img) ./ (nof_row_blocks, nof_col_blocks))
    row_slices = [ i*block_size[1] + 1 : (i+1) * block_size[1] for i in 0:nof_row_blocks-1]
    col_slices = [ i*block_size[2] + 1 : (i+1) * block_size[2] for i in 0:nof_col_blocks-1]

    blocks = [(x,y) for y in col_slices for x in row_slices]

    shuf = zeros(eltype(img), size(img))
    shuf_blocks = shuffle(blocks)

    for b in 1:length(blocks)
        shuf[shuf_blocks[b][1], shuf_blocks[b][2]] = img[blocks[b][1], blocks[b][2]]
    end
    shuf
end

# top-level code
Img = testimage("fabio")
New  = init_game(img)
imshow(new)
sleep(3)
println("code done..")

```

To run the file we just type: `$ julia PuzzleGame.jl` in the command line

You can think of it as if you open a new julia process and write all the statements from the file - this is why we need that “top-level” code.
after julia finish running all statements the process ends and the image window closes because it’s father process ends.
This is really Slow… If you remember the JIT compilation you will understand why it’s so slow, Julia actually compiles everything it read in the file (including the package we use)
You really should not use scripts, unless you have a good reason - maybe running on a server or if loading time is insignificant.

Most of the time a module will be the better way to work,
Meaning, we will use our julia prompt rather than the command line. Once you have opened a Julia REPL we save up the time for multiple julia process compilation  / module loading.

### Modules
Are actually encapsulated code keeping it’s inner namespace,
They help us create logical  blocks of code sharing outside, only what is relevant for the user to use (using export == public)
This enables us abstraction and re-use of functionality
To create a module we simply use the module key word, Julia style uses camel case for  module names and does not indent the code to save some indentaion :)
use end to finish the module (add a comment to specify the module name)

Julia is Actually Built on top of some standard modules:
+ Core - the core language is under core module
+ Base - is common used methods
+ Main - The main process running: This is actually the main REPL

This means that the module we will create is under Main



we can create our module now..
don’t leave top-level code ⇒ it will run when you load the module with using - we can use isinteracitve() to distinguish between running as a module (inter-active) or as a script


```julia
module PuzzleGame
using Images, ImageView, TestImages
using Random

export main

function init_game()..
end

function main()
	  img = testimage("fabio")
    new = init_game(img)
    guidict = imshow(new)
		println("main function complete")

end

# Top-level code
if !isinteractive()
	main()
  sleep(3)
	println("code done..")
end

end # module PuzzleGame
```

### Revise
Before we will load the new file to our REPL. make sure you open a fresh REPL so the workspace is clean. And we will add a new package- Revise
This package reduces the need of restarting Julia session which consumes a lot of time
It tracks modules and files - for example it replaces older versions cleanly
This improves the developing process when you add more changes to the module
Let’s load the model, and run our game.
```julia
includet ("./PuzzleGame.jl")
using .PuzzleGame  # using Main.PuzzleGame

main()
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

Alternivly we can use the import, which is used to specifically bring a method to scope
```julia
import .PuzzleGame.rules
rules()
```

So, we can start our game with our main function. And that’s our first step in really getting an interactive game
We covered for how to use modules any why are they when they are better than scripts.
Next we will cover the GUI - and see how to get information out of the imshow window.


---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")
