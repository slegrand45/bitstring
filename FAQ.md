# Programming #

1. How do I match an exact length bitstring?

Normally the bitmatch operator matches the prefix of a bitstring.  For instance:

```
  bitmatch bits with
  { foo : 1 } ->
```

will match 1 or more bits, binding the first bit to "foo".  To match only a single
bit, create a bitstring field at the end and check its length is zero, ie:

```
  bitmatch bits with
  { foo : 1 ;
    rest : -1 : bitstring }
      when Bitstring.bitstring_length rest = 0 ->
```

Note that the length of the "rest" field must be -1, meaning it will consume
all the rest of the input.  The above code is efficient (even though it might not
seem like it), just involving a couple
of arithmetic operations and the comparison with zero.

2. How do I parse a list of fields?

We don't have a syntax for matching arrays yet, although we may add one
in future.  To match a list of fields, probably the easiest way is to use a loop:

```
  let rec loop bits =
    bitmatch bits with
    | { foo : 4 ;
        rest : -1 : bitstring } ->
      do_something_with foo;
      loop rest
    | { rest : -1 : bitstring }
        when Bitstring.bitstring_length rest = 0 ->
      () (* end of list of fields *)
    | { _ } ->
      failwith "partial field found"
```

The above code is also efficient, because sub-bitstring extraction just updates a couple
of counters.  In other words, no copying or complex substring operations need to be
performed.

# Performance #

1. I want to use bitstring to inspect packets in my gigabit router design.  [Seriously, somebody actually asked this ...]

At the moment bitstring aims to be correct, not lightning fast.  Nevertheless it's also
pretty fast too.  Some things to watch out for:

  * Examine the generated code for your matches.  See the "print-examples" target in the Makefile for how to do this.
  * Profile your code to find out where it spends most of its time.
  * Certain operations are quite expensive.  Particularly Bitstring.string\_of\_bitstring, which can be called implicitly if you match against a string.  Also byte-aligned operations tend to be much faster than non-aligned operations (they usually go via different code paths).  Also, the more that is constant at compile time, the better we optimize, so avoid computed bit lengths / expressions where possible.
  * Bitstring internally uses safe string operations.  We will do this while bitstring is beta and until we are confident there are no bounds checking overflows.  You might wish to replace these operations with the unsafe equivalents.