### Intro

[![Julia tutorial p4](https://img.youtube.com/vi/7js2w1LIUQE/maxresdefault.jpg)](https://youtu.be/7js2w1LIUQE "Julia tutorial p4")

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


---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  yayo.prg@gmail.com
