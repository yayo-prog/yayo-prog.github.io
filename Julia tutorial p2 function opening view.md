## **_Intro_**

[![Julia tutorial p2](PATH TO THUMBNAIL)](PATH TO VID "Julia tutorial p2")
* Hi everyone welcome back to another julia language tutorial. We are working on our image puzzle game
* Last video we’ve gone through image basics and finished with some visualization of the image.
* In this video we will start using a more realistic image and we will create our opening view which is simply the scrambled image.
* More importantly we will create the first julia function - which is a great way for reuse code and get thing more generalized.


#### Toy example
we will start with a fresh (more realistic image) taken from TestImages.jl
```julia
using Images, TestImages, ImageView
img = testimage("fabio");
```
We can’t shuffle the image in-place without overriding the data we need, so we will allocate an empty (black) image to be our target (shuffled version).
now we can hard-code some of the "scrambling"

```julia
shuf = zeros(RGB, size(img))
size(img)
b1,b2 = 1:128, 129:256  # Julia indexing from 1
shuf[b2,b1] = img[b1,b1]
guidict = imshow(shuf, name="toy example")
```
#### Generalize:
That’s nice but we need generalize: that will also require the use of Random Package
```julia
using Random
nof_blocks = 4
block_size = size(img)[1] / nof_rows
slices = [ (i*block_size) + 1 : (i + 1)*block_size for i in 0:nof_blocks - 1 ]
blocks = [(x,y) for y in slices for x in slices]
reshape(blocks , nof_blocks, nof_blocks)  # This is just for view

shuf_blocks = shuffle(blocks)
```
  1. we can think of blocks as the slices in the origin image and shuf_blocks as the target of those slices - assigning each block from it's origin position to their target will create us the scrambled version we are looking for
  1. We can index the array by 2 indexing [r,c] or single index [ind]

	```julia
	blocks[3,2]  == blocks[7]
	```

	> note: Julia single indexing is a 'row major' - meaning first iterating all rows in the first column then continuing to the second col...


  2. we can iterate through and array with a single index with the where the last index in the array is `length(blocks)`
  ```Julia
	for b in 1:length(blocks)
		shuf[shuf_blocks[b][1], shuf_blocks[b][2] ] = img[blocks[b][1], blocks[b][2]]
	end
	```

#### Functions:
Function create a new local name-space meaning variables defined inside a function are not accessible from the global scope of the REPL.

to define a function we will use the `function` keyword
optional arguments are separated from the positional arguments with a semicolon

``` julia
function init_game(img; nof_blocks=4
    block_size = Int(size(img)[1] / nof_blocks)
    slices = [ i*block_size + 1 : (i+1) * block_size for i in 0:nof_blocks]

    blocks = [(x,y) for y in col_slices for x in row_slices]

    shuf = zeros(eltype(img), size(img))
    shuf_blocks = shuffle(blocks)

    for b in 1:length(blocks)
        shuf[shuf_blocks[b][1], shuf_blocks[b][2]] = img[blocks[b][1], blocks[b]
    end
    shuf
end
```


###### specific dispatch
  1. adding :: to each argument specifies the type of the argument creating a specific dispatch
  2. Using parametric method will create a specific dispatch method each time it will be called with a different colortype
  3. we also use broadcasting to create a block with a different height and width

	``` julia
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

	```
	> this is not a safe function, we need to make sure what will happen in case the size of the image is not divisible by the number of blocks
	>
	we can now see the difference between our two method in the help mode in the REPL  `help?> init_game` (enter a ? in the begining of the line to enter help mode)

+ Wow that’s really great progress, we’ve covered so much
Next we will show how we can start the game and we will talk about modules
Don’t forget to comment below and tell me what you think
It you like what i’m doing don’t forget to subscribe to get notify when a new video is released
Thanks
