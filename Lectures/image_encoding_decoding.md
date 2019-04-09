# Image Encoding / Decoding

## Images
At the most basic level, a digital image is a series of bits (0s and 1s).
How those bits are translated into the visual image is dependent on the 
format of the image file.  Formats include JPG, GIF, TIFF, PNG, BMP, etc.

In general, formats include information such as:
  * Are pixels stored by rows or by columns
  * Bit depth, or how many bits are required to define each pixel
    + Greyscale:  each pixel has a base10 value of 0 to 255 (8 bits)
    + Color:  each pixel has values for Red, Green, Blue, each of 0 to 255, or
    24 bits
  * Additional compression of the data
  
But, whatever format is used, the image is represented by a series of binary 
numbers.

## Encoding / Decoding
Encoding is taking a data format and changing it into some other
format that is more readily shared with other programs or across the web.

For example, we have been using JSON encoding to take native Python data 
structures such as lists and dictionaries and "encoding" them into JSON strings 
that can be sent over the internet and interpreted by other programs.

Decoding is simply the reverse.

### Encoding binary data
In the binary (base2) system, I have two symbols for representing numbers.
Take the binary (base 2) number 101011.  If this translated directly into a
string for sending over the web, it would be "101011".  This is 6 characters
long.

__base10__

base10, of course, uses ten symbols (0, 1, 2, 3, 4, 5, 6, 7, 8, 9) to
represent numbers.  If we "encode" this binary number 101011 into the base10 
system, its value is "43".  As a string, this would be only two characters 
long, and hence shorter to transmit.

__ base16__

base16 adds 6 additional symbols (a, b, c, d, e, f) for representing numbers.
The binary number 101011 translates to "2b" in base16, also known as 
hexadecimal.

__base64__
 
The [base64](https://en.wikipedia.org/wiki/Base64#Base64_table) system uses
64 characters to represent numbers.  These symbols include upper case letters,
lower case letters, numbers, and various punctuation.  The binary number 101011 
translates to "r".  

As we increase the number of characters or symbols used to represent numbers,
the number of characters needed to represent a particular number decreases.  
When sending as a string over the web, smaller is better.

## Encoding an image 
The binary number series of an image can be converted into a base64 string for
transmission over the internet.  Python has a built-in module called `base64`
that contains encoding and decoding functions for base64.  It is a built-in
module, and so while it needs to be `imported` into code, it does not need to
be separately installed in a virtual environment.

Below is sample code for using `base64` for encoding and decoding an image.

```
import base64
import io
from matplotlib import pyplot as plt
import matplotlib.image as mpimg


def read_file_as_b64(image_path):
    with open(image_path, "rb") as image_file:
        return base64.b64encode(image_file.read())


def view_b64_image(base64_string):
    image_bytes = base64.b64decode(base64_string)
    image_buf = io.BytesIO(image_bytes)
    i = mpimg.imread(image_buf, format='JPG')
    plt.imshow(i, interpolation='nearest')
    plt.show()
    return
    
    
def save_b64_image(base64_string):
    image_bytes = base64.b64decode(base64_string)
    with open("new-img.jpg", "wb") as out_file:
        out_file.write(image_bytes)
    return


if __name__ == '__main__':
    b64_img = read_file_as_b64("D:\dwonl\Pictures\Sight.jpg")
    view_b64_image(b64_img)
    save_b64_image(b64_img)
```
The file encoding is demonstrated in the `read_file_as_b64()` function.  
  * First,a binary image file is opened using the `image_file` variable.
  * `image_file.read()` reads in the bytes from the image file.
  * `base64.b64encode` takes the bytes from the image file and encodes them
  into a base64 string.
  
The base64 string could now be sent to or returned by a web server as a JSON.

The base64 string can then be decoded, as demonstrated by the `view_b64_image()`
function.
  * The `base_64_string` received as an argument is decoded using 
  `base64.b64decode()`.  It saves the resulting bytes in a variable 
  `image_bytes`.
  * In this example, instead of saving these bytes to a file on disk, they are
  put into an IO stream using the `io.BytesIO()` function.  This function takes
  the bytes and converts them into an IO stream in memory that acts like a 
  file.  This IO stream is referenced by the `image_buf` variable.
  * The image from the `image_buf` is read in by the `matplotlib` function
  `mpimg.imread`.  The expected format of the image is given so the image
  interpreter will understand the format of the bytes.
  * Finally, `plt.imshow()` and `plt.show()` are used to display the image.

The process for saving the base64 string into an image file on disk is
demonstrated by the `save_b64_image()` function.
  * The base64 decoding is done the same as in the `view_b64_image()` function.
  * The resulting bytes are then written to a binary file. 

## Documentation Links
* <https://docs.python.org/3/library/base64.html>