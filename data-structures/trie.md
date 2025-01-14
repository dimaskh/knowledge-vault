# Trie (Prefix Tree)

Last Updated: 2025-01-14

## Overview
A Trie is a tree-like data structure that stores a dynamic set of strings. Tries excel at string operations, particularly those involving prefixes.

## Properties
1. Root node is empty
2. Each node stores a character
3. Each path from root represents a string
4. Nodes may share prefixes
5. Usually has end-of-word markers

## Basic Implementation

### Node Structure
```python
class TrieNode:
    def __init__(self):
        self.children = {}  # or [None] * 26 for lowercase letters
        self.is_end = False
        self.count = 0  # Number of words with this prefix
```

### Trie Class
```python
class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
            node.count += 1
        node.is_end = True
    
    def search(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        return node.is_end
    
    def starts_with(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        return True
    
    def count_prefix(self, prefix):
        node = self.root
        for char in prefix:
            if char not in node.children:
                return 0
            node = node.children[char]
        return node.count
```

## Advanced Operations

### Delete Operation
```python
def delete(self, word):
    def _delete_helper(node, word, depth=0):
        if depth == len(word):
            if not node.is_end:
                return False
            node.is_end = False
            return len(node.children) == 0
        
        char = word[depth]
        if char not in node.children:
            return False
        
        should_delete = _delete_helper(node.children[char], word, depth + 1)
        
        if should_delete:
            del node.children[char]
            return len(node.children) == 0
        
        return False
    
    _delete_helper(self.root, word)
```

### Autocomplete
```python
def autocomplete(self, prefix):
    def _find_words(node, prefix, words):
        if node.is_end:
            words.append(prefix)
        
        for char, child in node.children.items():
            _find_words(child, prefix + char, words)
    
    node = self.root
    for char in prefix:
        if char not in node.children:
            return []
        node = node.children[char]
    
    words = []
    _find_words(node, prefix, words)
    return words
```

### Longest Common Prefix
```python
def longest_common_prefix(self):
    def _find_lcp(node, prefix):
        if not node.children or len(node.children) > 1:
            return prefix
        if node.is_end:
            return prefix
        
        char = next(iter(node.children))
        return _find_lcp(node.children[char], prefix + char)
    
    return _find_lcp(self.root, "")
```

## Memory-Efficient Implementation

### Compressed Trie
```python
class CompressedTrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False
        self.prefix = ""  # Store shared prefix

class CompressedTrie:
    def __init__(self):
        self.root = CompressedTrieNode()
    
    def insert(self, word):
        node = self.root
        while word:
            # Find matching prefix
            matched = False
            for key in node.children:
                i = 0
                while i < len(key) and i < len(word) and key[i] == word[i]:
                    i += 1
                
                if i > 0:
                    if i == len(key):
                        word = word[i:]
                        node = node.children[key]
                    else:
                        # Split existing node
                        old_key = key[i:]
                        new_node = CompressedTrieNode()
                        new_node.children[old_key] = node.children[key]
                        del node.children[key]
                        node.children[key[:i]] = new_node
                        word = word[i:]
                        node = new_node
                    matched = True
                    break
            
            if not matched:
                # Add new node
                new_node = CompressedTrieNode()
                new_node.is_end = True
                node.children[word] = new_node
                break
```

## Time Complexity
- Insert: O(m) where m is word length
- Search: O(m)
- Delete: O(m)
- Space: O(ALPHABET_SIZE * m * n) where n is number of words

## Use Cases

1. Autocomplete Systems
   - Search suggestions
   - Text prediction
   - IDE code completion

2. Spell Checkers
   - Dictionary implementation
   - Error correction
   - Word validation

3. IP Routing Tables
   - Prefix matching
   - Network routing
   - Packet forwarding

4. Phone Directories
   - Contact search
   - Prefix-based search
   - Number completion

## Advanced Applications

### Pattern Matching
```python
def pattern_match(self, pattern):
    def _match_helper(node, pattern, index):
        if index == len(pattern):
            return node.is_end
        
        if pattern[index] == '.':
            return any(_match_helper(child, pattern, index + 1)
                      for child in node.children.values())
        
        if pattern[index] not in node.children:
            return False
        
        return _match_helper(node.children[pattern[index]], pattern, index + 1)
    
    return _match_helper(self.root, pattern, 0)
```

### Word Break
```python
def word_break(self, s):
    n = len(s)
    dp = [False] * (n + 1)
    dp[0] = True
    
    for i in range(1, n + 1):
        for j in range(i):
            if dp[j] and self.search(s[j:i]):
                dp[i] = True
                break
    
    return dp[n]
```

## Best Practices

1. Implementation
   - Choose appropriate character set
   - Handle case sensitivity
   - Consider memory usage

2. Optimization
   - Use compressed tries for space
   - Implement efficient deletion
   - Cache frequent lookups

3. Usage
   - Preprocess dictionary
   - Handle special characters
   - Consider trade-offs

## Common Pitfalls

1. Implementation Issues
   - Memory overflow
   - Incorrect end marking
   - Poor deletion handling

2. Performance Issues
   - Not compressing when needed
   - Inefficient character storage
   - Poor cache utilization

3. Design Issues
   - Wrong character set choice
   - Missing error handling
   - Incorrect prefix handling
