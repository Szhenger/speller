# Speller

## Problem to Solve

Given any file, this program `speller` spell-checks that file, using a hash table with dictionary operations. 

## Background

Spell-checking is a fundamental task in text processing, requiring algorithms that can efficiently compare input words against a pre-defined dictionary. When designing such algorithms, it's essential to consider not only their asymptotic complexity but also their real-world performance. For example, while two algorithms with time complexities of O(n) and O(2n) are asymptotically equivalent, in practice, the latter could feel twice as slow due to the constant factor of 2.

This spell-checker program is designed to prioritize wall-clock time, i.e., the actual time experienced by the user when running the program, rather than just the theoretical efficiency of the algorithm. To achieve this, the program focuses on optimizing both the loading of the dictionary into memory and the checking of words against the dictionary, ensuring that it runs as efficiently as possible in real-world scenarios.

The spell-checker consists of two main components:
* `speller.c`: The main program that handles the reading of an input file, running the spell-check, and providing the results.
* `dictionary.c`: Responsible for managing the dictionary of valid words, including loading the dictionary from disk and checking whether a word is correctly spelled.
These components are connected via `dictionary.h`, a header file that contains function prototypes shared between `speller.c` and `dictionary.c`. This modular structure allows for more organized and maintainable code, especially as the program grows in complexity.

The challenge of this program lies in implementing an efficient dictionary loading mechanism and a fast spell-checking algorithm that can handle large input files without noticeable delays, ensuring that the program runs quickly and efficiently even on large datasets.

## Understanding

`dictionary.h`:
* Preprocessor Directives:
    * At the beginning of `dictionary.h`, you'll notice lines that mention `#ifndef DICTIONARY_H`, `#define DICTIONARY_H`, and `#endif`. These are include guards, which ensure that even if dictionary.h is included multiple times in different files, it will only be processed once during compilation. This prevents any redefinition errors or conflicts, especially in larger projects where multiple files interact with the same header.
* Including Standard Libraries:
    * The line `#include <stdbool.h>` might be new if you're unfamiliar with it, but it plays a crucial role in defining the bool type in C. In this program, the bool type is used to return true/false values from various functions, such as whether a word exists in the dictionary or if the dictionary was loaded successfully.
* Constants with `#define`:
    * You'll also see `#define LENGTH 45`, which defines a macro called `LENGTH` that represents the maximum length of any word in the dictionary. This is a preprocessor directive, which means that every time the code references `LENGTH`, it is automatically replaced with the value 45 before compilation. This constant helps maintain consistency across the codebase and prevents accidental modification of the word length limit.
* Function Prototypes:
    * Finally, `dictionary.h` defines the prototypes for five key functions that are implemented in `dictionary.c`. These functions are essential for interacting with the dictionary:
        * `check`: This function will take a word as input and determine if it exists in the loaded dictionary.
        * `hash`: This function maps a word to a specific bucket in a hash table, enabling efficient lookups.
        * `load`: This function loads the dictionary into memory from a file.
        * `size`: This function returns the number of words in the loaded dictionary.
        * `unload`: This function frees up the memory used by the dictionary when the program is done.


## Specification

TODO
