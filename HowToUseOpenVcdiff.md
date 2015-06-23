# How to use `open-vcdiff` #

## Introduction ##
`open-vcdiff` is an encoder and decoder
that can write and read the VCDIFF format described in
[RFC 3284 : The VCDIFF Generic Differencing and Compression Data Format](http://www.ietf.org/rfc/rfc3284.txt).
The encoder uses the
[Bentley/McIlroy technique](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.11.8470&rep=rep1&type=pdf)
for finding matches between the
source and target data.

An encoder/decoder named
[Xdelta](http://xdelta.org),
which also reads and writes the VCDIFF format, has already
been released by Josh MacDonald under the GNU
General Public License v2.
`open-vcdiff` is released under the
[Apache 2.0 license](http://www.apache.org/licenses/LICENSE-2.0),
which makes it suitable for use in applications that will not
necessarily be released as open-source.

The primary purpose for `open-vcdiff` is to be included in
implementations of the Shared-Dictionary Compression over HTTP
(SDCH, or "Sandwich") protocol.  Please join the
[SDCH Google Group](http://groups.google.com/group/SDCH)
if you want to find out more about SDCH.

_Note:_ In this document, and in the source code,
the term "dictionary" is used interchangeably with the term "source"
(or "source file" or "source data") as defined in RFC 3284.

## Extensions to standard VCDIFF format ##
`open-vcdiff` conforms to the
[VCDIFF draft standard](http://www.ietf.org/rfc/rfc3284.txt)
described in RFC 3284.  The following non-standard extensions to
the format can be enabled if desired:

  * **Interleaved format.**  The VCDIFF draft standard format divides each encoded delta window into three sections (data, instructions, and addresses), with the aim of improving compressibility of the encoded file using a secondary compressor such as gzip.  The drawback to this approach is that none of the target data can be reconstructed unless the entire delta window is available. The delta window is received in packets over the network and it is desirable to be able to process its contents as they arrive.  In order to facilitate decoding a stream of packets from the network, we have modified the VCDIFF format so that it interleaves the data, instructions, and addresses instead of placing them in three separate sections.  Each instruction is followed by its size and then by an address or literal data.  This feature is enabled by passing the format flag `VCD_FORMAT_INTERLEAVED` to the C++ encoder interface, or by specifying `-interleaved` on the `vcdiff` command line.
  * **Adler32 checksum.**  The format can be modified to include an [Adler32](http://en.wikipedia.org/wiki/Adler-32) checksum of the target window data.  If the checksum format is used, then bit 2 (0x04, defined as `VCD_CHECKSUM`) of the Win\_Indicator byte will be set, and the checksum will appear just after the "Length of addresses for COPYs" field and before the "Data section for ADDs and RUNs" section in the encoding.  The decoder will verify the decoded target data against the checksum, if it is present.  This feature is enabled by passing the format flag `VCD_FORMAT_CHECKSUM` to the C++ encoder interface, or by specifying `-checksum` on the `vcdiff` command line.  This checksum format is **not** compatible with the Adler32 checksum used by Xdelta.
  * **Version header byte (Header4).** If either of the two enhancements described above is used, then the resulting format will not conform to the VCDIFF draft standard as described in RFC 3284.  In order to indicate this deviation from the standard, the fourth byte in the encoding (Header4, reserved for the VCDIFF version code) will be set to 0x53 (a capital "S" character in ASCII.)   If neither enhancement is used, the fourth byte may be 0x00 (a null character), the default value described in the standard.

## System requirements ##

The package has been built and tested using the [Autoconf](http://www.gnu.org/software/autoconf)/[Automake](http://www.gnu.org/software/automake) build system on Red Hat Linux, Ubuntu Linux, Cygwin on Windows, Mac OS X, and Solaris 10, using gcc versions 3.2.2, 3.4.4, and 4.0.3.  Other flavors of Unix may require some minor changes.

On Microsoft Windows, you can build the package using either [Cygwin](http://www.cygwin.com) or Visual Studio 2005.  See below for details on the latter.

Of course, the authors will be delighted to receive submissions of patches that will add support for additional operating systems and compilers.

Some notes about porting `open-vcdiff` to specific environments follow.

### Microsoft Visual Studio 2005 ###

Solution and project files for Microsoft Visual Studio 2005 are provided in the `vsprojects` directory of the CVS source tree (but not the source tarball.)

### Mac OS X ###

In order to build `open-vcdiff` on OS X, you will need to download and install [Xcode](http://developer.apple.com/tools/xcode/) if it is not already installed on your machine.

### Solaris ###

On Solaris 10, if you run across a build error
"`libstdc++.la is not a valid libtool archive`", please refer to the following
Sun forum post for a workaround:
http://forums.sun.com/thread.jspa?forumID=844&threadID=5073150

## Installing the package ##

See the `INSTALL` file for (generic) installation instructions for C++:
basically:
```
./configure
make
sudo make install
```

`make` will compile and link the `open-vcdiff` libraries
and unit tests as well as `vcdiff`, a simple
command-line
utility to run the encoder and decoder.  `make install`
will create a `google` subdirectory under `/usr/local/include`
if it does not already exist.

## Running `vcdiff` from the command line ##

Typical usage of `vcdiff`
is as follows
(the `<` and `>` are file redirect
operations, not optional arguments):
```
vcdiff encode -dictionary file.dict < target_file > delta_file
vcdiff decode -dictionary file.dict < delta_file > target_file
```

To see the command-line syntax of vcdiff, use `vcdiff -help`
or just `vcdiff`.

## Calling the open-vcdiff libraries from C++ code ##

### Quick Summary ###

To call the encoder from C++ code, assuming that dictionary, target,
and delta
are all `std::string` objects:
```
#include <google/vcencoder.h>  // Read this file for interface details
[...]
  open_vcdiff::VCDiffEncoder encoder(dictionary.data(), dictionary.size());
  encoder.SetFormatFlags(open_vcdiff::VCD_FORMAT_INTERLEAVED);
  encoder.Encode(target.data(), target.size(), &delta);
```

Calling the decoder is just as simple:
```
#include <google/vcdecoder.h>  // Read this file for interface details
[...]
  open_vcdiff::VCDiffDecoder decoder;
  decoder.Decode(dictionary.data(), dictionary.size(), delta, &target);
```

When using the encoder, the C++ application must be linked with the
library options `-lvcdcom` and `-lvcdenc`; when
using the decoder, it must be linked with `-lvcdcom` and `-lvcddec`.

### Interface Details ###

The preceding examples use the simple interface to the encoder and decoder,
which assume that the entire target file to be encoded, or the
entire delta file to be decoded, is immediately available.  There
is also a streaming interface which can be used when the target or
delta data is received incrementally.

Encoding target files using the streaming encoder involves the
following steps:
  * Include the header file `<google/vcencoder.h>`.
  * Load the dictionary into memory.  If the dictionary is stored in a file, this can be done with the Unix system call `mmap`.
  * Create a `HashedDictionary` object using the dictionary address and length.
    * A pointer to this object can be retained and used for many encoding operations.  A pointer to a single `const HashedDictionary` object can be shared and used concurrently by multiple encoding threads.
    * Call the `Init()` method on the `HashedDictionary` object after creating it.
  * Create a `VCDiffStreamingEncoder` object using the `HashedDictionary` object plus the following additional arguments.
    * A set of format extensions for the encoder to use.  Standard format is represented by `VCD_STANDARD_FORMAT`.  To use interleaved format and/or checksum format, use the format flags `VCD_FORMAT_INTERLEAVED`, `VCD_FORMAT_CHECKSUM`, or `VCD_FORMAT_INTERLEAVED | VCD_FORMAT_CHECKSUM`.
    * The parameter `look_for_target_matches` controls whether the encoder will look for target matches within the previously encoded target data.  In our testing, we have found that it is best to set this parameter to `false` if `gzip` is to be applied to the delta file after VCDIFF encoding.
  * For each target file to be encoded:
    * Create a string object in which to store the delta encoding.  This is normally a `std::string`.  With some specialization of the `OutputString` template class, the output string can be any type that supports `append()`, `size()`, etc.  See the header file `google/output_string.h` for details.
    * Call the `StartEncoding()` method on the `VCDiffStreamingEncoder` object.
    * Loop through reading as much target data as possible.  Each time more data arrives, call `EncodeChunk()`, and process any delta data that has been appended to the output string.
    * When all target data is exhausted, call `FinishDecoding()` and process any additional delta data that has been appended to the output string.
    * If any of these methods returns false, an error has occurred and has been logged to stderr.  In that case, do not continue with the encoding operation.
  * Remember to link the code with the library options `-lvcdcom` and `-lvcdenc`.

Likewise, decoding delta files using the streaming decoder involves the
following steps:
  * Include the header file `<google/vcdecoder.h>`.
  * Load the dictionary into memory.  If the dictionary is stored in a file, this can be done with the Unix system call `mmap`.
  * Create a `VCDiffStreamingDecoder` object.
  * For each delta file to be decoded:
    * Create a string object in which to store the decoded target.  This is normally a `std::string`, but see the encoder instructions above which describe how to use a different type.
    * Call the `StartDecoding()` method on the `VCDiffStreamingDecoder` object using the dictionary address and length.
    * Loop through reading as much delta data as possible.  Each time more data arrives, call `DecodeChunk()`, and process any target data that has been appended to the output string.
    * When all delta data is exhausted, call `FinishDecoding()` and process any additional target data that has been appended to the output string.
    * If `DecodeChunk()` or `FinishDecoding()` returns false, an error has occurred and has been logged to stderr.  In that case, do not continue with the decoding operation.
  * Link the code with the library options `-lvcdcom` and `-lvcddec`.

For simple examples of how to use the streaming encoder and decoder, please see
the comments in the header files `google/vcencoder.h` and `google/vcdecoder.h`.
For an example of a full application that uses these interfaces, please see the
source file `vcdiff_main.cc`, included in this package, which implements the
command-line client.

## Running the unit tests ##

To verify that the package works on your system, especially after
making modifications to the source code, please run the unit tests
using `make check`.
If you find that the unit tests fail on your system without having made
any changes to the code, please contact
[opensource@google.com](mailto:opensource@google.com).

## Coding Style ##

The
[Google C++ Style Guide](http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml)
has been followed as much as possible within
this package, and we ask contributors to familiarize themselves with
those guidelines and follow them when making modifications to the code.

## Contact us ##

The authors can be reached by e-mail at
[opensource@google.com](mailto:opensource@google.com).