### INTRO
Hi everyone, welcome back to another julia tutorial video
I wasn’t uploading for a while now. So i’m sorry.
I hope i can go back into shape :)

So I recently saw a paper about earth map…

It was written by :
J. Richard Gott III, David M. Goldberg, Robert J. Vanderbei
And it’s supposed to be better than the `Winkel Tripel` 

* For some background this [map](https://www.google.com/search?q=winkel+tripel&sxsrf=ALeKk03SkhriMuPmWwv7932LSmtXQH1dOQ:1615491288043&tbm=isch&source=iu&ictx=1&fir=ckWYyFfzBrq6xM%252ClrYJwgwuDNiegM%252C%252Fm%252F025_3v5&vet=1&usg=AI4_-kRqw7uXHwWl-F4Vc2iy36fA19gXsQ&sa=X&ved=2ahUKEwjx2fGC_qjvAhWdUhUIHTZgAe8Q_B16BAg6EAE#imgrc=ckWYyFfzBrq6xM) was introduced at 1921
* It is called tripel because Winkel minimized a triple distortion loss - area, direction and distance.
* This [new map](https://arxiv.org/abs/2102.08176) minimized 6 types of distortion, but we want to get into that...

What caught me, is the fact that at the end of the paper they suggested to print the 2 halves of the map and glue them together…
Who does this?!?!

So today we are trying to play a bit with these maps…
I took the images directly from the paper and saved them to disk.


###  Preparation:
Lets’ load the images and scale them down a bit, so the manipulation will run faster.
> **_Dowload:_** You can download the images from my gith hub:\
> [north hemisphere](https://github.com/yayo-prog/julia_tutorials/blob/master/flat_earth_north.png)\
> [south hemisphere](https://github.com/yayo-prog/julia_tutorials/blob/master/flat_earth_south.png)\
> Or download directly from the [paper](https://arxiv.org/pdf/2102.08176 "paper in arXiv")


```julia
using Images
using ImageTransformations

north = load("/home/shefi/Pictures/flat_earth_north.png");
south = load("/home/shefi/Pictures/flat_earth_south.png");

# Scale the images
scale = 384  # 128*3
north_s = imresize(north, scale, scale);
south_s = imresize(south, scale, scale);

println("North: $(size(north))  small version $(size(north_s))")
println("South: $(size(south))  small version $(size(south_s))")
```

### Ilustration
Now, I saw some [illustration](https://www.princeton.edu/news/2021/02/15/princeton-astrophysicists-re-imagine-world-map-designing-less-distorted-radically) for the map, but i didn’t liked it.

I want to see where the disks meet.

* So We will stat by gluing the two disks together, North and South.
```hcat(north_s, south_s)```
* For the vertical `vcat(north_s, south_s)`
* But this wont work…
We need to rotate it a bit..
`imrotate(south_s, + π, axes(south_s));`
* Now we can think of rotating the two disks togther together:

```julia
function flat_horizontal_map(az::Int, north_s, south_s)
    rad = az * (2π/360);

    # keeps the axes - this is great for a cicular image
    north_rot = imrotate(north_s, rad, axes(north_s));
    south_rot = imrotate(south_s, -rad, axes(south_s));

    flat = hcat(north_rot,south_rot)
end
```



### Making a Video
So we have our basic functionality. To make a video we first need to wrap a sequence of images together:
```
frames = []
for az in 100:1:520
    flat_earth = flat_horizontal_map(az)
    frame = convert.(RGB, flat_earth)
    # annotate!(1.,1.,az_to_lat(az))
    push!(frames, frame)
end
```
> **_NOTE:_**  I downloaded the images with an Alpha channel so i need to convert a RGB.


Now we can close the to a video:
```julia
using VideoIO
props = [:priv_data => ("crf"=>"22","preset"=>"medium")]
encodevideo("flat_map.mp4", frames, framerate=25, AVCodecContextProperties=props)
```
*  I’m not sure about the properties, but hey, this works…

That’s it!


Actually I started playing with these image in a more inter active mode.
I used Pluto. Which should be familiar if you are up to date with last julia con.
If not this will be my next Video.
See you soon 

---
[Home](/index "all tutorial")    &emsp;&emsp;&emsp;    [youtube]( https://youtu.be/6-HXx5Hbtak "Video")  &emsp;&emsp;&emsp;  [mail me](mailto:yayo.prg@gmail.com "yayo.prg@gmail.com")