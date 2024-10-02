# Speller

## Problem to Solve

Given any file, this program `speller` spell-checks that file, using a hash table with dictionary operations. 

## Background

Spell-checking is a fundamental task in text processing, requiring algorithms that can efficiently compare input words against a pre-defined dictionary. When designing such algorithms, it's essential to consider not only their asymptotic complexity but also their real-world performance. For example, while two algorithms with time complexities of `O(n)` and `O(2n)` are asymptotically equivalent, in practice, the latter could feel twice as slow due to the constant factor of 2.

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
* At the top of `dictionary.c`, a struct called `node` is defined. This struct represents a `node` in the hash table, and each node will store:
    * A `char` array to hold a word from the dictionary.
    * A pointer to the next `node`, which is essential for resolving collisions in the hash table (when two or more words hash to the same index).
* By using a linked list structure, each hash bucket can hold multiple words, ensuring that even if two words hash to the same value, both can still be stored.
* A global pointer array named table is declared, representing the actual hash table. The hash table is an array of pointers, where each element in the array points to the head of a linked list (a chain of node structs) containing the words that hash to the same index.
* The size of this array is determined by `N`, which is initially set to 26 (to 28) in line with the sample hash function, where each word is hashed based on its first letter (A-Z). However, depending on your hash function’s design, you may want to increase N to optimize performance by distributing the words more evenly across the table and reducing collisions.
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
* The increased number of buckets (`N = 28`) also helps spread the words more evenly, which further improves the spell-checker’s real-world performance by keeping lookup times low, even with large dictionaries.

`speller.c`:
The speller.c file serves as the main driver for the spell-checking program. Its primary purpose is to coordinate the process of loading a dictionary, checking the spelling of words in a text file, and reporting any misspellings along with relevant performance statistics. Let’s walk through its functionality:
* Program Structure and Usage:
    * The `speller.c` file is structured to handle two key inputs:
        * Dictionary file: A list of lowercase words, one per line, used to check against the input text. The dictionary can either be provided as a command-line argument or defaults to `dictionaries/large` if no dictionary is specified.
        * Text file: The file to be spell-checked, which contains the words that will be compared to the loaded dictionary.
* Loading the Dictionary:
    * Dictionary Loading: The program starts by calling the load function from `dictionary.c`, which reads the dictionary file and loads all of its words into memory. If the dictionary cannot be loaded (because load is not yet implemented or there’s an error), the program will display an error message.
    * The default dictionary (`dictionaries/large`) contains 143,091 words, which are all lowercase and sorted lexicographically. During development, a smaller dictionary (`dictionaries/small`) is provided to help with debugging. The dictionary must be efficiently loaded into memory so that the spell-checking process can run quickly.
* Spell-Checking the Text File:
    * Checking Words: After loading the dictionary, speller.c reads the text file word by word and passes each word to the check function (implemented in dictionary.c). The check function determines if each word is correctly spelled by searching for it in the dictionary. Any word not found in the dictionary is flagged as a misspelling.
    * The program is designed to handle large text files efficiently, but during development, it’s recommended to use smaller text files for testing to avoid overwhelming memory structures with large datasets.
* Benchmarking Performance:
    * Timing Execution: One of the key features of speller.c is its use of the getrusage function, which allows for benchmarking (i.e., timing) the performance of the core functions from `dictionary.c` — `load`, `check`, `size`, and `unload`. This ensures that your implementations are not only functionally correct but also efficient in terms of real-world execution time.
    * The benchmarked functions are:
        * `load`: The time it takes to load the dictionary into memory.
        * `check`: The time it takes to check the spelling of each word in the text file.
        * `size`: The time it takes to return the number of words in the loaded dictionary.
        * `unload`: The time it takes to free up all memory used by the dictionary.
By benchmarking these functions, you can identify any performance bottlenecks in your implementation and optimize them to improve the overall speed of the program.
* Reporting Misspellings and Statistics:
    * After checking all the words in the text file, the program reports:
        * Misspellings: Each word from the text file that was not found in the dictionary.
        * Statistics: The program also prints out key performance metrics such as:
            * The total number of misspelled words.
            * The number of words in the dictionary.
            * The number of words in the text file.
            * The total execution time for loading, checking, and unloading the dictionary.
* These statistics help you evaluate the effectiveness of your implementation, both in terms of accuracy (correctly identifying misspelled words) and performance (how quickly the spell-checker processes files).
* Flow:
    * Load the dictionary.
    * Open the text file and read it word by word.
    * For each word, call check to see if it exists in the dictionary.
    * Report any misspellings.
    * Output performance statistics, including the number of misspelled words and the execution time for key functions.
    * Unload the dictionary from memory.

## Specification
The spell-checker program processes an input text file and compares its words against a dictionary of valid words. The dictionary is loaded into memory from a file, and the program checks each word in the input text, identifying any misspelled words. The spell-checker provides performance statistics on the dictionary loading and spell-checking process.

#### Command-line Usage:
* `./speller [dictionary] src.txt`.
* `dictionary` (optional): The path to a dictionary file containing a list of lowercase words, one per line. If omitted, the program defaults to `dictionaries/large`.
* `src.txt`: The path to the text file to be spell-checked.

#### Functionality:
* Load Dictionary:
    * Reads the dictionary file into memory, storing each word in a hash table to allow for efficient lookups.
    * If the dictionary cannot be loaded, an error is displayed.
* Spell-Check Text File:
    * Reads the input text file word by word, using the check function to determine if each word is spelled correctly.
    * Words not found in the dictionary are reported as misspelled.
* Performance Benchmarking:
    * The program measures and reports the time taken to load the dictionary, check the text, and unload the dictionary from memory.
    * Statistics:
        * Outputs the total number of misspelled words, the total number of words in the dictionary, and the total number of words in the text file.
        * Displays the execution time for loading the dictionary, checking the words, and unloading the dictionary.

## Credit and Disclaimer

This problem originates from [CS50's Introduction to Computer Science](https://cs50.harvard.edu/x/2024/psets/5/speller/) at Harvard University with the `speller.c` made by the CS50 Team and any solution here is explicitly for educational purposes only.
