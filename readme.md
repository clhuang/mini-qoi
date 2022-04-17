<img align="left" src="mini_qoi_logo.png" width="120px">

**Mini QOI (abbreviated mQOI) is a streaming QOI decoder, designed for embedded systems with very little RAM.**

<br><br><br>

---

## Why choose mQOI?

+ It requires no dynamic memory allocation!
+ It only needs about 300 bytes of RAM while decoding an image!
+ It supports decoding an incoming QOI stream byte-by-byte!

**If you're making an embedded app that makes use of the `.qoi` format and need a simple encoder, this is probably for you!**

## Why not choose mQOI?

- It's probably slightly slower than the reference codec.

**If you're making an application that needs the most performance possible, this probably isn't for you.**

## Example build instructions

To build the example app, use CMake:

```sh
cd examples
mkdir build
cd build
cmake ..
cmake --build .
```

Now the `examples/build directory` should contain `mqoi_decoder`, a simple app that decodes a `.qoi` image and stores it as a file in the RGBA8 format.

## Minimal code example

This is a minimal example with no error handling. Assume that `get_next_byte()` is a function that returns the next byte in the QOI file.

```c
#include "mini_qoi.h"

// image metadata decoding

mqoi_desc_t img_desc;
uint32_t img_w, img_h;

mqoi_desc_init(&img_desc); // initialize the image descriptor object

while (!mqoi_desc_done(&img_desc)) { // while the image descriptor isn't complete,
    mqoi_desc_push(&img_desc, get_next_byte()); // read one byte and push it to the image descriptor 
}

mqoi_desc_verify(&img_desc, &img_w, &img_h); // read the image width and height

// image decoding

mqoi_dec_t dec;
mqoi_rgba_t * px;

mqoi_dec_init(&dec, img_w * img_h);

while (!mqoi_dec_done(&dec)) {
    mqoi_dec_push(&dec, get_next_byte()); // read one byte and push it to the image decoder 

    while ((px = mqoi_dec_pop(&dec)) != NULL) { // as long as the decoder has pixels to read
        // do whatever you want with the pixel here
    }
}

// done!

```