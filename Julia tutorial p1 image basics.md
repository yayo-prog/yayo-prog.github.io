## **_Intro_**


[![Watch the video](https://yt-embed.herokuapp.com/embed?v=aml-hVfg7XU)](https://youtu.be/aml-hVfg7XU "Images basics video")


* Hi everyone, welcome to Julia language tutorial.  We are building a small image puzzle game. During this time we will cover julia concepts and some useful packages.

* Thinking of our game we need some fundamentals in order to build it.
Loading an image
Visualize the image
Annotate the image - mark some metadata on it
Basic editing of the image
* So this will be what we will cover today.


### **_Using:_**
In our into video we’ve created an environment and added some packages to it
   1. Activate our env
   2. ] status for checking which are our loaded package

   3. Now we can use these packages, to bring the package to scope we run: ``` julia> using Images, ImageView, TestImages```
   4. [Precompilation](https://stackoverflow.com/questions/40116045 "exteinson on pre-compilation: stackoverflow") - dynamic language means:
    * All top-level statements are executed at precompile-time, instead of at load-time.
    * Any statements that are to be executed at load-time must be moved to the __init__ function.
   5. [Images package](https://juliaimages.org/latest/pkgs/#page_packages_index-1 "packages under Images") is actually a collection of some other useful packages (Colors, ImageAxes…) - this is easy for use but in case you intended on developing your own package it’s suggested to use the specific packages: ImagesCore…



### **_Load image:_**
  1. I have prepared the julia logo image (png) format
   ```julia
   logo = load("~/Downloads/julia_logo.png")
   ```
  2. Julia always returns the last executed code, and represents it on the REPL. So What we see is the representation of the image as it was stored in the ‘logo’ variable but not the image itself..
  3.  Julia represents an image as a 2D array of pixels;  in our case it’s a 4 channel RGBA, those pixels can also be a 1 channel -  in a grayscale image or 3 channels in simple RGB.
    + This may be a bit confusing for those coming from other languages, But in my opinion it is really the correct way of thinking of images,
    + We can see the full 4 channels with several options:
          1. channelview function `chnl_v = channel_view(logo)` expands each pixel channels to the first dimension (to convert back: `colorview(chnl_v)`)

          use `permutedims(chnl_v, [2,3,1]) )` to arrange the color channels in the third type.

          `PermutedDimsArray(chnl_v)` - to only use pointer rather than copy.

         2. using `red.(logo)` / `green.(logo)`.. to extract a  specific channel

     + N0f8 - is a fixed point numerical representation. this avoid the need to convert int to double and creates numeric errors


### **_Visualize:_**
ImageView is the package we need for viewing the image, if we work with Juno / Jupyter you will see it directly (show method is overloaded)
  1. open a new window showing the image:
  ```Julia
  guidict = imshow(logo, name="My Image")
  ```
  2. Will cover more of the fields in the dictionary in future videos, for now we can understand the concept of the dictionary holding multiple variables accessible by “keys” rather specific order
  3. Let’s write something on the image:
   ```Julia
   idx = annotate!(guidict, AnnotationText(col, row, "something", color=RGB(0,0,1), fontsize=15))
  ```
  4. we can change the annotation type to draw other shapes
  5. The annotation is part of the GUI not the image itself, hence it wont change the image itself if want to save it
  6. to delete the annotaion `delete!(guidict, idx)`
  > note: ! - denotes an "in-place" method editing the first argument rather than creating a copy.

### **_Editing the image:_**
editing an image or changing the values of certain pixels can be done in several ways
  1. **Rectangles or slices**
  ```julia
  rect_cp = logo[181:200, 81:180]
  rect_v = @view logo[101:120, 81:180]
  ```
  2. default assignments creates a copy of the slice, so Images gives us @view macro to set a pointer.
  4. Fill the entire rect - using fill or .= , colorant is a syntactic sugar returning different colors
  > .= (and any 'dot' notation is broadcasting - applying the function to each element sepearatly )

  ```julia
  rect_cp .= RGB(0,0,1)
  rect_v .= colorant"magenta"
  imshow!(guidict, logo);
  ```
  5. [Colorant](https://github.com/JuliaGraphics/ColorTypes.jl "color type") is the highest abstract type representing all kind of colors
  6. We can see the difference in the image only for the view object which is a pointer to the same memory location
  7. **Mask** - can be for non adjacent pixels
  8. Mask is a binary representation of the image, for example:
  ```julia
  dots = (blue.(logo) .> 0.7) .& (red.(logo) .< 0.7 )
  ```
  9. We can use it to edit all pixels with those conditions (and refresh the image to the canvas)
  ```Julia
  logo[mask] .= colorant"black"
  imshow!(guidict["gui"]["canvas"], logo);
  ```
  10. We can alter the binary matrix to a list of cartesian index and use them in assigning new values (a mater of preferences)
  ```julia
  ll = findall(red_dot)
  logo[ll] .= colorant"black"
  ```

* I think those very simple manipulations are sufficient for now, and we can start our little project.
* Next topic we will cover is how to create our opening view of the game and we will use functions for that.

---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube channel](https://www.youtube.com/playlist?list=PLfH1V5m5U7OyEHo82rQSuhzM_NPKubeb8 "My Channel")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")
