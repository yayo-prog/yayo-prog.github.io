layout: page
title: "Julia tutorial part 6 - struct"
permalink: /tutrials/p6

### Intro

<!-- [![Julia tutorial p5](https://yt-embed.herokuapp.com/embed?v=vBUDqeqq4Xc)](https://youtu.be/vBUDqeqq4Xc "Julia tutorial p5") -->

Hi everyone, welcome to julia language tutorial.
Last time we created our swap and annotate logic of our image puzzle game
Our game really starts to look better and actually, it’s almost done...
In This video we will refactor our code a bit mainly for performance reasons and also for better separation of code logic.
We will see how to use composite types - aka struct

### Struct:
Our incentive really comes from the first  ‘TO DO’ in the julia performance tips - which is actually **not** to do - don’t use globals. But we currently have some `state` data being calculated in several functions.
We can actually wrap it all in one variable and pass this variable as an argument between all our functions.


Composite types are created by struct keyword, this creates a new type defined by the user. **This is not** an object and is not associated with inner methods.
+ When declared, a struct is by default unchangeable after creation, to enable changing it use `mutable struct`.
+ As we mentioned, to avoid all our global declarations, we would like to collect all inner game state into one object, and pass it to all functions that need to change that obje	ct, to save all global declarations..
```julia
mutable struct GameState{T <: Colorant}
    img::Array{T,2}
    shuf::Array{T,2}
    guidict::Dict{String,Any}
    isActive::Bool
    firstClick::Bool

    nof_blocks::Tuple{Int,Int}
    block_size::Tuple{Int,Int}

    box_handler::Union{Nothing,ImageView.AnnotationHandle{ImageView.AnchoredAnnotation{AnnotationBox}}}
    swaps::Int64

    function GameState(img::Array{K,2}, nof_blocks::Tuple{Int,Int}) where K <: Colorant
        shuf, block_size = init_game(img, nof_row_blocks=nof_blocks[1], nof_col_blocks=nof_blocks[2])
        guidict = imshow(shuf, name="Image Puzzle Game")
        new{K}(img, shuf, guidict, true, true, nof_blocks, block_size, nothing,  0)
    end
end
```

+ Declaring the GameState with respect to T - means that this is actually a parametric type.  And our GameState structure is defined by the type of the image. E.g : GameState{RGB}

+ It is easier to create the constructors for the type inside the struct block but outer declarations is also legit (the new() function applies only in the constructor who lives inside the struct declaration)



### Multi-dispatch:
We can (and should) declare specific dispatches for the struct, e.g. the method show from base
to overload new dispatches from Base first import the specific function: ` import Base.show`
Let’s create a special call for printing our game state
This is a good practice, and also will help us debug the code if needed
Import Base.show
function show(io::IO, game::GameState)
    print("Game $(game.isActive ? "Active" : "Ended"). $(game.swaps) have been conducted and currently on $(game.firstClick ? "First" : "second") click")
end
function show(io::IO, ::MIME"text/plain", game::GameState)
    print("Game $(game.isActive ? "Active" : "Ended"). input image size $(size(game.img))")
end
2 argument method is the one used in print / repr
The 3 arguments method is the one used when you time an instance of GameState in the REPL `game_state`

Refactoring:
To better use our new structure rather than global variables, it is more convenient  to first refactor the ‘do’ syntax into a callback function and pass our new structure as one of the arguments.
Create the new callback function - make sure to pass the gameState.
Check the exact type of the passed signal - in our case : `Reactive.Signal` holding: `GtkReactive.MouseButton{GtkReactive.UserUnit}`
Because `map` expects a signal argument we can create a partial function - meaning a pointer to a version of the function with exactly one argument.
``` julia
function f1(a,b)
	# do something
end
f1(a) = f(a, b_val)
```
In our case we will create `do_click` function that gets two arguments, one it the `MouseButton` and the second is our `GameState`
```julia
function do_click(btn::GtkReactive.MouseButton{GtkReactive.UserUnit}, game_state::GameState)
#With all of the click logic

```
And we will create a mapping and change the call to:
```julia
partial_do_click(btn::GtkReactive.MouseButton{GtkReactive.UserUnit}) = do_click(btn, game_state)
    coord_sig = map(partial_do_click ,guidict["gui"]["canvas"].mouse.buttonpress, init=(0.0,0.0))

    GtkReactive.gc_preserve(guidict["gui"]["window"], coord_sig)  # prevents the mapping from being garbage collected

```
+ We added gc_preserve to avoid the garbage collector to delete our created coord_sig

### Game ending:
Lastly we need to check if we solved the game. In this case we would like to exit the game with a victory feeling.
I’m a big fan of ascii art , so i HAVE to use some banner package
Lets add FIGlet  and use it to congrats our ending of the game
```julia
Pkg.add(“FIGlet”)
Using Figlet
```
and we can add the finish code to the do_click function (so we will re-evalute it each time)
```julia
 if gameState.img == gameState.shuf
    println(“YOU WON!!!”)
    FIGlet.render(“you won” , “Larry 3D”)
    game.isAcitve = false
end
```

### Conclusion
We have seen how to use struct and how can we create struct specific methods by overloading them with another dispatch.
Next time we will continue to improve our game and learn more about Julia




---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")
