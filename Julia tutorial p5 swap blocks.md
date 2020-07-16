### Intro

[![Julia tutorial p5](https://yt-embed.herokuapp.com/embed?v=vBUDqeqq4Xc)](https://youtu.be/vBUDqeqq4Xc "Julia tutorial p5")

### Intro
Hi everyone, welcome to julia language tutorial.
Last time we created our first interaction with our game
In This video we will build the core functionality of the game which is to swap tiles around

### Coords to block:
Last time we saw the values returned from the mouse click signals. we need to translate our sub-pixel accuracy to a specific block
Get the block using reminder / div  (one is the reminder of the division and the second is the intege part of the divison)
```julia
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
```

Now we can add the function to the signal mapping.
It is  recommend to use @guarded macro before all signal connection, this should help us avoid messing up the Gtk inner state (this is basically a try/catch block) and avoid the need to restart our julia session.
we added the init value so when the game is loaded the mapping will not return the default -1,-1 by setting an init user-defined value.

```julia
coord_sig = @guarded map(guidict["gui"]["canvas"].mouse.buttonpress, init=(0.0,0.0)) do btn
        global ann_handler, firstClick
        row = Float64(btn.position.x)
        col = Float64(btn.position.y)
        println("you clicked @ [x=$(row), y=$(col)] with $(btn.button)")
        margins = get_block_coords((row, col), block_size)
```

+ In the future we will refactor this code to a different function: the type of the button is :   Reactive.Signal{GtkReactive.MouseButton{GtkReactive.UserUnit}}

### Annotate:
After getting the coordinates surrounding the click - we can mark it!
```julia
left, top right, bottom = margins
ann_handler = annotate!(guidict, AnnotationBox(left,top,right,bottom, linewidth=2.5, color=colorant”blue”))
firstClick = false
```
To access the annotation later - we need to define it as global - so it will not be deleted each function call (the values are stored as float)
```julia
global ann_handler, firstClick
ann_handler.ann.data.left
```

All of this code should be under the ` if firstClick` - in addition , we need to change the state so in the next click(and function call) we will count for second click clause.
+ If you follow along all the tutorials, don't forget to change the top-level init for `firstClick = true` or the game will crash trying to swap an undefined first block.


### Swap blocks
Create a temp array to swap the tiles, tmp array is garbage collected when function ends and used only in the scope of the function
by passing the image we actually pass a pointer to the image
```julia
"""
box = Int.([left, top, right, bottom])
"""
function swap_blocks(boxA, boxB, img)
    tmp = img[boxA[2]:boxA[4], boxA[1]:boxA[3]]
    img[boxA[2]:boxA[4], boxA[1]:boxA[3]] = img[boxB[2]:boxB[4], boxB[1]:boxB[3]]
    img[boxB[2]:boxB[4], boxB[1]:boxB[3]] = tmp
    return img
end
```
 We can add this functionality under the else clause
```julia
swap_boxes(boxA, boxB, new)
delete!(guidict, ann_handler)
firstClick = true
```



### Conclusion
We are almost done with our puzzle game.
Next we will work on some performance tips and show how can we use structs
And of course we will finish the game.



---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")
