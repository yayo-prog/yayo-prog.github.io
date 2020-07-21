layout: page
title: "Julia tutorial part 6 - struct"
permalink: /tutrials/p6

### Intro
[![Julia tutorial p6](https://yt-embed.herokuapp.com/embed?v=M0I2qZnI8Bc)](https://youtu.be/M0I2qZnI8Bc "Julia tutorial p6") 

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

```

### Conclusion
We have seen how to use struct and how can we create struct specific methods by overloading them with another dispatch.
Next time we will continue to improve our game and learn more about Julia

---
#### code so far:
```julia
module PuzzleGame

using Images, ImageView, TestImages
using Gtk, Reactive, GtkReactive
using Random
import Base.show

export main

mutable struct GameState{T <: Colorant}
    img::Array{T,2}
    shuf::Array{T,2}
    guidict::Dict{String, Any}

    isActive::Bool
    firstClick::Bool

    nof_blocks::Tuple{Int,Int}
    block_size::Tuple{Int,Int}

    box_handler::Union{Nothing,ImageView.AnnotationHandle{ImageView.AnchoredAnnotation{AnnotationBox}}}
    swaps::Int

    function GameState(img::Array{K,2}; nof_blocks::Tuple{Int,Int}=(4,4)) where K <: Colorant
        shuf, block_size= init_game(img, nof_row_blocks=nof_blocks[1], nof_col_blocks=nof_blocks[2])
        guidict = imshow(shuf, name="Image Puzzle Game")
        new{K}(img, shuf, guidict, true, true, nof_blocks, block_size, nothing, 0)
    end
end

function show(io::IO, game::GameState)
    print("Game $(game.isActive ? "On-Goning" :  "Ended") conducted $(game.swaps) swaps")
end
function show(io::IO, ::MIME"text/plain", game::GameState)
    print("3 arg metod Game $(game.isActive ? "On-Goning" :  "Ended")")
end

function rules()
    println("please swap tiles to complete the image")
end

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
    shuf, block_size
end

"""
get the margins of the block from (x,y) pixel location
"""
function get_block_coords(coord::Tuple{AbstractFloat, AbstractFloat}, block_size::Tuple{Int,Int})
    offset = rem.(coord, block_size)
    left = coord[1] - offset[1] + 1
    top = coord[2] - offset[2] + 1

    right = left + block_size[1] - 1
    bottom = top + block_size[2] - 1

    margins = Int.(round.([left, top, right, bottom]))
    return margins
end

"""
box = Int.([left, top, right, bottom])
"""
function swap_blocks(boxA, boxB, img)
    tmp = img[boxA[2]:boxA[4], boxA[1]:boxA[3]]
    img[boxA[2]:boxA[4], boxA[1]:boxA[3]] = img[boxB[2]:boxB[4], boxB[1]:boxB[3]]
    img[boxB[2]:boxB[4], boxB[1]:boxB[3]] = tmp
    return img
end


function do_click(btn::GtkReactive.MouseButton{GtkReactive.UserUnit}, game_state::GameState)
    row = Float64(btn.position.x)
    col = Float64(btn.position.y)
    println("you clicked @ [x=$(row), y=$(col)] with $(btn.button)")
    margins = get_block_coords((row, col), game_state.block_size)

    if game_state.firstClick
        println("highlight the selected block")
        game_state.box_handler = annotate!(game_state.guidict, AnnotationBox(margins[1], margins[2], margins[3], margins[4],
                                linewidth=2.5, color=colorant"blue"))
        game_state.firstClick = false

    else
        println("swap the blocks")
        prev_margins = Int.([game_state.box_handler.ann.data.left, game_state.box_handler.ann.data.top, game_state.box_handler.ann.data.right, game_state.box_handler.ann.data.bottom])
        swap_blocks(margins, prev_margins, game_state.shuf)
        delete!(game_state.guidict, game_state.box_handler)
        game_state.swaps += 1
        game_state.firstClick = true
    end
    return row,col
end


function main()
    img = testimage("fabio")
    game_state = GameState(img)
    rules()

    @guarded signal_connect(game_state.guidict["gui"]["window"], :destroy) do widget
        game_state.isAcitve = false
        println("you closded the window")
    end

    partial_do_click(btn::GtkReactive.MouseButton{GtkReactive.UserUnit}) = do_click(btn, game_state)
    coord_sig =  map(partial_do_click, game_state.guidict["gui"]["canvas"].mouse.buttonpress, init=(0.0,0.0))
    return game_state
end

#Top level code
if !isinteractive()
    state.main()
    while state.isAcitve
        sleep(1)
    end
    println("code done..")
end

end # module PuzzleGame
```


---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")
