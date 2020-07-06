**_Julia language introduction:_**

[!Watch the video](https://img.youtube.com/vi/Vy4vu_coH9A/maxresdefault.jpg)(https://www.youtube.com/Vy4vu_coH9A)

+ Hi everyone welcome to my julia language series. When I found out about Julia I thought the language was super cool, I mean it’s really  FAST and at first glance it was like just python to me. Everything looked straight forward to code ,  
But… well, you know that’s not always the case, and there are tweaks that you need to know if you want to really program in any language.
Hopefully this series will be all about that - the “know how” of Julia.
*Julia language  has a really  growing community, and the packages and their githubs are excellent, and the julia-con videos are really interesting but for me i found that there aren’t many “hands on” tutorials, and that's kinda what i like...

* I think my inspiration comes mainy from the sentdex channel which I love and use frequently. And I wanted to also create and share something for others to use.

* I know that Julia had it’s reputation for inconsistency in prior versions (mainly before 1.x) but it really comes from the rapid growth of the language.  I'll be using Julia 1.4 for this series which is a stable version.


+ Trying to break down Julia to its main features and where does it get it speen from:
  * Dynamic language - meaning you can work interactively, that's called a REPL (Read Eval Print Loop), and it’s really nice developing this way.
  * JIT compilation - which means Julia actually compiles everything it reads, for example  function definition and so on, and that makes it fast.
  * Multi-dispatch - a way of definig julia function, they can (but not mandatory) be defined with a specific arguments type, this again help the compiler to allocate the memory better and helps the speed
  * It’s NOT an object oriented language, in my perspective it comes copouled with multi-dispatch  paradigm. Meaning you should define a specific method(dispatch) of the function for different variable types, rather than implementing them in the object


*As is said, I think the best way to learn is by simply rolling out your sleeves and start coding. I encourage you all to follow along the examples I’ll do or simply use the snippets for your own project. I will also upload a text version of these videos to github so you can choose your format

* So for the first project I thought of building A simple “Image Puzzle Game”. Where you get a scrambled image, that is an image that was broken into tiles but the tiles were misplaced, the player's goal is to arrange those pieces by swapping them around, to get the complete image.
Pretty simple right…
I think that will give us a good base to explore the best in julia as well as some important Packages

* Let’s start - download Julia



**Install Julia:**
We’ll do the long way installation. use can download the binaries from julialang [site](https://julialang.org/downloads/ "Donwland latest juila") or ust wget
```bash
wget https://julialang-s3.julialang.org/bin/linux/x64/1.4/julia-1.4.2-linux-x86_64.tar.gz
tar -xvzf julia-1.4.2-linux-x86_64.tar.gz
sudo cp -r julia-1.4.2 /opt/
sudo ln -s /opt/julia-1.4.2/bin/julia /usr/local/bin/julia
```

* I’m using a linux machine, specifically ubuntu 18.04.
Actually i think 18.04 actually support julia with apt install, but this way we can specify the exact version
* If you are using Windows pc donwload the .exe file and follow the [instructions](https://julialang.org/downloads/platform/ "platform specific instructions") , after installation everything should run the same.
* My background mainly involves python and computer vision so some of the features i’ll work with will be images related.

* I think beginner programers can also follow along these tutorials, and mainly learn via examples
*Please comment what you think below, that will help me improve my content for you.

* I’ll be coding mainly through the basic REPL, if you can I recommend working with Juno - which is an IDE based on the atom editor.
this requires Atom.jl , IJulia.jl, Juno.jl



* Let’s open our REPL (Read Eval Print Loop) environment
  * This is where you can run julia code
  * Some of the different modes of the repl
* Packages: part of the julia ecosystem is the different packages we can add.
  * Julia comes with it’s built in package manager which is called Pkg.
  * We can use it in the code after calling ```julia> using Pkg``` or enter package mode by ```]``` in the beging of the line in the REPL
  * Activate an environment, to be more encapsulated and this want mess with other packages and dependencies
    ```julia
    (@v1.4) pkg> activate .
    (@v1.4) pkg> add Images, ImageView, TestImages, ImageMagick
    ```
And add Images, ImageView, TestImages, ImageMagick
* See you in the next video to start working with those packages
