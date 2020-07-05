**_Intro_**
* Hi everyone welcome back to another julia language tutorial. We are working on our image puzzle game
* Last video we’ve gone through image basics and finished with some visualization of the image.
* In this video we will start using a more realistic image and we will create our opening view which is simply the scrambled image.
* More importantly we will create the first julia function - which is a great way for reuse code and get thing more generalized.


1. Toy example
```julia
img = testimage("fabio");
```
We can’t shuffle the image on the spot without overriding data we need, so we will allocate an empty image for our shuffled version

```julia
shuf = zeros(RGB, size(img))
size(img)
B1,b2 = 1:128, 129:256  # Julia indexing from 1
shuf[b2,b1] = img[b1,b1]
guidict = imshow(shuf, "toy example")
```
2. **_Generalize:_** That’s nice but we need generalize: that will also require the use of Random Package
```julia
using Random
nof_rows = 4
block_size = size(img)[1] / nof_rows
row_slices = [ (i*block_size) + 1 : (i + 1)*block_size for i in 0:nof_rows - 1 ]
col_slices = row_slices
blocks = [(x,y) for y in col_slices for x in col_slices]
reshape(blocks , nof_rows, nof_rows)  # This is just for view
```
  1. We can index the array by 2 indexing [r,c] or single index [ind] ```juila
	 length(blocks)
	 ```
	 will the size by one number
  2. Iterate all the tiles
3. **_Function:_**
  1. Using parametric method which is defined for those type of arguments
  2. after the semicolon - optional arguments with default values
		```julia
	function init_game(img::Array{T}; nof_rows::Int=4) where T<:Colorant
		Row_slices = ...
		Blocks = [(x,y) for y in col_slices for x in row_slices]
		Blocks = reshape(blocks, ..)

		Shuf = zeros(.eltype(img), ...)
		shuf_blocks = shuffle(blocks)

		For b in 1:length(blocks)
			Shuf[shuf_blocks[b][1] , .... = img[blocks[b][1]...]
		End
	end
	```

Wow that’s really great progress, we’ve covered so much
Next we will show how we can start the game and we will talk about modules
Don’t forget to comment below and tell me what you think
It you like what i’m doing don’t forget to subscribe to get notify when a new video is released
Thanks


FLOW
Ramp up
Toy example- 2 block per row


Generalize tiles (square power of 2)

Function
With types
