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

`dictionary.c`: We define the data structures and algorithms responsible for loading, storing, and accessing the dictionary of words. This file forms the backbone of the spell-checking functionality by implementing a hash table that allows for efficient lookups of words in the dictionary. Let’s break down some of the key components:
`node` struct:
* At the top of `dictionary.c`, a struct called `node` is defined. This struct represents a `node` in the hash table, and each node will store:
    * A `char` array to hold a word from the dictionary.
    * A pointer to the next `node`, which is essential for resolving collisions in the hash table (when two or more words hash to the same index).
* By using a linked list structure, each hash bucket can hold multiple words, ensuring that even if two words hash to the same value, both can still be stored.
`table` array:
* A global pointer array named table is declared, representing the actual hash table. The hash table is an array of pointers, where each element in the array points to the head of a linked list (a chain of node structs) containing the words that hash to the same index.
* The size of this array is determined by `N`, which is initially set to 26 (to 28) in line with the sample hash function, where each word is hashed based on its first letter (A-Z). However, depending on your hash function’s design, you may want to increase N to optimize performance by distributing the words more evenly across the table and reducing collisions.
Hash Function:
* The hash function is implemented in `dictionary.c`, although the initial version is fairly simple, using only the first letter of the word to compute its hash value. Specifically, it converts the first letter to lowercase and maps it to an index between 0 and 25 (for a to z).
* While this approach works, it creates clusters of words in the same bucket (especially if many words start with the same letter), which can slow down lookups. To improve efficiency, the hash function has been redesigned to better distribute words across the 28 hash buckets. Instead of using only the first letter of each word, this hash function takes the ASCII average value of all characters in the word, calculates the average, and then applies the modulus operation (`% N`) to determine the correct bucket.
* This method ensures that words are distributed more evenly across the hash table by considering every character in the word, rather than just the first letter. The average of the ASCII values helps to spread words with similar starting letters (like "apple" and "axe") into different buckets, reducing the number of collisions.
Function Implementations:
* With the hash function implemented, the other core functions (load, check, size, unload) use the hash table efficiently to perform dictionary operations:
    * `load`: Reads words from the dictionary file, computes the hash value of each word using the new hash function, and inserts the word into the corresponding bucket in the hash table.
    * `check`: Hashes the input word and searches the appropriate bucket for a match. It scans the linked list in the bucket to determine if the word exists in the dictionary.
    * `size`: Returns the total number of words loaded into the hash table.
    * `unload`: Frees the memory allocated for each node and clears the hash table when the program is finished.
    * `size`: Returns the total number of words loaded into the dictionary.
    * `unload`: Frees all memory used by the hash table, ensuring there are no memory leaks when the program finishes executing.
Optimization Considerations:
* The updated hash function enhances the distribution of words, reducing the number of collisions and speeding up the lookup process. By incorporating the ASCII values of all characters in the word, rather than just the first letter, this approach minimizes the chance that many words will hash to the same index, even if they share similar starting letters.
* The increased number of buckets (N = 28) also helps spread the words more evenly, which further improves the spell-checker’s real-world performance by keeping lookup times low, even with large dictionaries.

* ## Specification

TODO
