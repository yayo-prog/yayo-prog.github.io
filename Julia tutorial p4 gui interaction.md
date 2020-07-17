### Intro

[![Julia tutorial p4](https://yt-embed.herokuapp.com/embed?v=7js2w1LIUQE)](https://youtu.be/7js2w1LIUQE "Julia tutorial p4")

Hi everyone, welcome to julia language tutorial.
Last time we saw how to launch the game and how to use modules.
In This video we will interact with the gui properties created with imshow.
Imshow is based on Gtk and Reactive packages. The important  thing to understand is that imshow actually does a lot of the work for us - creating all the components of the window.
Today we will start looking under the hood of imshow and check some of the components to see what can we do with them.


### Gtk widget and signal
For the most simple interaction, we want to let the program know when we closed the window - or gave up..
First we will load some needed packages
```julia
using Pkg
Pkg.add("Gtk")
Pkg.add("Reactive")
Pkg.add("GtkReactive")
using Gtk, Reactive, GtkReactive
```
We can use gtk special signal for it
[gtk](https://developer.gnome.org/gtk3/stable/ "gtk developer docs") is a cross platform toolkit used for building GUI, Gtk is written in C and Gtk.jl is the julia api for it.
Gtk works with two main objects - widgets  and events. Widgets are the graphical objects and the events are signals coming from the widgets.
We can connect signals(events) from the window - for example the destroy window event - we will use it to change the state of the game

let's add the connection of the signal to our main function
```julia
signal_connect(guidict["gui"]["window"], :destroy) do widget
    println("Window closed - finishing game")
   end
```
in this "do" syntax we define a new function (with widget as it's only argumet) that will run when the window gets destroyed (another method for singnal_conncect is to pass the function to the first argument)

### Signals:
This is implemented through the Reactive package and the main concept is that the values are time variants and we need to process them in different times.
**DO NOT** be confused with signal_connect in the Gtk
Simple Signal
Derived signal - is a more complexed signal - that is a function of another signal, BUT it’s also a signal. this means that whenever the origin signal value is changes the derived signal get re evaluted and gets it's new value.
For our usage we will see that the mouse actually is represented by a signal of some type and we will use it

When i know what i need, and can't fine a specific solution online i try to invesegatge the properties in some of the may-used packages.
best method for that are: (typeof + fieldnames + ?)
```
typeof(guidict["gui"]["canvas"].mouse
fieldnames(ans)
?MouseHandler
```
we can define a mapping for button press and put in under main function
```julia
sig_click = map(guidict["gui"]["canvas"].mouse.buttonpress) do btn  # Reactive.Signal{GtkReactive.MouseButton{GtkReactive.UserUnit}}
    x_curr = Float64(btn.position.x)  #row
    y_curr = Float64(btn.position.y)  # col
    println("Found a button clicked @ [x=$(round(x_curr, digits=2)), y=$(round(y_curr, digits=2))]")
    If firstClick
        println(“mark block”)
    Else
        println(“make swap”)
```
firstClick should be defined in our "top-level" code and to be `firstClick= true`
so we will enter our logic with 'first click'

Great! Now let’s define the framework

code so far:
```julia

module PuzzleGame

using Images, ImageView, TestImages
using Gtk, Reactive, GtkReactive
using Random

export main

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
    shuf
end

function main()
    img = testimage("fabio")
    new = init_game(img)
    guidict = imshow(new)
    rules()

    signal_connect(guidict["gui"]["window"], :destroy) do widget
        isAcitve = false
        println("you closded the window")
    end

    coord_sig = map(guidict["gui"]["canvas"].mouse.buttonpress) do btn
        row = Float64(btn.position.x)
        col = Float64(btn.position.y)
        println("you clicked @ [x=$(row), y=$(col)] with $(btn.button)")

        if firstClick
            println("highligth the selected block")
        else
            println("swap the blocks")
        end

        return row,col
    end

    return guidict
end

#Top level code
firstClick = false
isActive = true
if !isinteractive()
    main()
    while isAcitve
        sleep(1)
    end
    println("code done..")
end

end # module PuzzleGame


```

---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")
