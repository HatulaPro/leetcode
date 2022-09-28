# 🟩 [Word Pattern](https://leetcode.com/problems/word-pattern/) 

## Description:

> Given a pattern and a string s, find if s follows the same pattern.
> 
> Here follow means a full match, such that there is a bijection between a letter in pattern and a non-empty word in s.

### Starting point:

```js
/**
 * @param {string} pattern
 * @param {string} s
 * @return {boolean}
 */
var wordPattern = function(pattern, s) {
    
};
```

## The Solution

So, my goal is to assign every letter in `pattern` a word in `s`. The first thing that came to my mind is to simply split the sentence by spaces. That would give my access to every individual word:

```js
const wordsArray = s.split(' ');
```

Splitting is has an O(n) time complexity, but since we are iterating over each letter/word anyway, it is going to be O(n) either way.

> ### NOTE:
> We could also implement a custom `split` function that would (probably) be more optimized for this specific task, but for the sake of simplicity I chose not to.

Since we know every word is assigned one and only one letter in `s`, we can check that their lengths match (if they don't - we know such correlation does not exist, and can return false).

```js
if (pattern.length !== wordsArray.length) return false;
```

Now starts the juicy part. Since both arrays (`pattern` is also an array - an array of characters) have the same number of items, we can iterate over both and check how they map to one another. 

Again, we want to make sure every letter maps to one unique word. This is exactly what maps are for. Maps allow us to access items in an O(1) time complexity, so that is exactly what we need.

We will iterate over our `pattern`. If a letter has not been seen before, we will assign the current word to it. Otherwise, we'll make sure the current word is the same as the word we previously assigned to the current letter. 

```js
const map = new Map(); // Init the map

for (let i = 0; i < pattern.length; i++) { // Going over every word/letter
    const currentLetter = pattern[i];
    const currentWord = wordsArray[i];
    if (map.has(currentLetter)) { // If currentLetter was seen before
        if (map.get(currentLetter) !== currentWord) return false; // If the "old" word is not the same as the current word, we know the pattern does not match
    } else {  
        map.set(currentLetter, currentWord); // Assigning the word to the letter
    }
}
```

Now, we only have one thing left to do. As you probably noticed, the same word can be assigned to multiple different letters. To make sure that does not happen, I added a set of previously seen words:

```js
const map = new Map(); 
const usedWords = new Set();

for (let i = 0; i < pattern.length; i++) { 
    const currentLetter = pattern[i];
    const currentWord = wordsArray[i];
    if (map.has(currentLetter)) { 
        if (map.get(currentLetter) !== currentWord) return false;
    } else {  
        if (usedWords.has(currentWord)) return false; // If word has been seen before, but does not match the current letter, return false
        
        map.set(currentLetter, currentWord); 
        usedWords.add(currentWord); // Add the word to the list of seen words
    }
}
```

Now, since we've detected every falsey case, we can safely return true:

```js
/**
 * @param {string} pattern
 * @param {string} s
 * @return {boolean}
 */
var wordPattern = function(pattern, s) {
    const wordsArray = s.split(' ');
    if (pattern.length !== wordsArray.length) return false;
    
    const map = new Map();
    const usedWords = new Set();
    
    for (let i = 0; i < pattern.length; i++) {
        const currentLetter = pattern[i];
        const currentWord = wordsArray[i];
        if (map.has(currentLetter)) {
            if (map.get(currentLetter) !== currentWord) return false;
        } else {
            if (usedWords.has(currentWord)) return false;
            map.set(currentLetter, currentWord);
            usedWords.add(currentWord);
        }
    }
    
    return true;
};
```