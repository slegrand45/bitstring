The ocaml-bitstring project adds Erlang-style bitstrings and matching over bitstrings as a syntax extension and library for OCaml.

(This project was formerly known as "bitmatch").

You can use this module to both parse and generate binary formats, files and protocols.

Bitstring handling is added as primitives to the language, making it exceptionally simple to use and very powerful.

Here is how to parse the header from a GIF image:

```
  let bits = Bitstring.bitstring_of_file "image.gif" in
  bitmatch bits with
  | { ("GIF87a"|"GIF89a") : 6*8 : string; (* GIF magic. *)
      width : 16 : littleendian;
      height : 16 : littleendian } ->
      printf "%s: GIF image is %d x %d pixels" filename width height
  | { _ } ->
      eprintf "%s: Not a GIF image\n" filename
```

This example shows how to parse the first few fields in a Linux ELF binary (see <elf.h>):

```
  let bits = Bitstring.bitstring_of_file "/bin/ls" in
  bitmatch bits with
  | { 0x7f : 8; "ELF" : 24 : string; (* ELF magic number *)
      e_ident : 12*8 : bitstring;    (* ELF identifier *)
      e_type : 16 : littleendian;    (* object file type *)
      e_machine : 16 : littleendian  (* architecture *)
    } ->
    printf "This is an ELF binary, type %d, arch %d\n"
      e_type e_machine;
```

Bitstring handles integers, strings, sub-bitstrings, big-, little- and native-endianness, signed and unsigned types, variable-width fields, fields with arbitrary bit alignment.

## Community, mailing list, discussion, chat ##

There is a mailing list for discussion of any issues around bitstring:
http://groups.google.com/group/bitstring

For immediate help and support, join the [#ocaml channel on Freenode](http://freenode.net/).

## Documentation ##

  * [Bitstring API documentation](http://et.redhat.com/~rjones/bitstring/html/Bitstring.html)
  * [Test suite coverage report](http://et.redhat.com/~rjones/bitstring/coverage-report/)

## Download ##

Bitstring is commonly available on Linux distros and other operating systems which have OCaml.  To download the source [go to our downloads page](http://code.google.com/p/bitstring/downloads/list).