# Lexd

A lexicon compiler for non-suffixational morphologies.

This module compiles lexicons in a format loosely based on hfst-lexc and produces transducers in ATT format which are equivalent to those produced using the overgenerate-and-constrain approach with hfst-twolc (see [here](http://wiki.apertium.org/wiki/Morphotactic_constraints_with_twol) and [here](http://wiki.apertium.org/wiki/Replacement_for_flag_diacritics)).

See [Usage.md](Usage.md) for the rule file syntax.

To build, do
```bash
./autogen.sh
make
make install
```

To run, do
```bash
lexd lexicon_file att_file
```

To get a speed comparison, do
```bash
make test
```

## Why is it faster?

When dealing with prefixes, the overgenerate-and-constrain approach initially builds a transducer like this:

![transducer that overgenerates](https://github.com/mr-martian/lexd/raw/master/imgs/lexc.png)

Then composes that with a twolc rule to turn it into somehting like this:

![correct transducer](https://github.com/mr-martian/lexd/raw/master/imgs/twoc.png)

But compiling the rule needed to do that can take hundreds of times longer than compiling the lexicon.

Lexd, meanwhile, makes multiple copies of the lexical portion and attaches one to each prefix, thus generating the second transducer directly in a similar amount of time to what is required to generate the first one.

| Language |  Wamesa | Hebrew | Navajo | Lingala |
|---|---:|---:|---:|---:|
| Stems |  262 | 127 | 19 | 1470
| Total forms | 12576 | 2540 | 473 | 1649496
| Path restrictions | 14 | 10 | 17 | 19 
| **Lexc + Twolc**
|    Lexc compilation | 25ms | 15ms | 25ms | 230ms 
|    Twolc compilation | 10245ms | 1360ms | 8460ms | 275525ms
|    Rule composition | 2050ms | 225ms | 1705ms | 45550ms 
|    Minimization | 65ms | 5ms | 20ms | 155ms |
|    Total time | 12385ms | 1605ms | 10210ms | 321460ms |
| **Lexd**
|    Lexd compilation | 210ms | 85ms | 10ms | 490ms |
|     Format conversion | 30ms | 5ms | 5ms | 55ms |
|     Total time | 240ms | 90ms | 15ms | 545ms |
|   **Speedup** | 52x | 18x | 681x | 590x |
