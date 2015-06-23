An encoder and decoder for the format described in [RFC 3284](http://www.ietf.org/rfc/rfc3284.txt): "The VCDIFF Generic Differencing and Compression Data Format."
The encoding strategy is largely based on [Bentley-McIlroy 99](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.11.8470&rep=rep1&type=pdf): "Data Compression Using Long Common Strings."
A library with a simple API is included, as well as a command-line executable that can apply the encoder and decoder to source, target, and delta files.
A slight variation from the draft standard is defined to allow chunk-by-chunk decoding when only a partial delta file window is available.

This implementation requires that the entire contents of the source file (also known as dictionary file) be loaded into memory.  Therefore it cannot handle source files larger than 2-4 GB, depending on the particular restrictions of the OS.  The target and delta file sizes do not have this limitation.

The current version of open-vcdiff is available for download at Google Drive:
http://goo.gl/jSelXK