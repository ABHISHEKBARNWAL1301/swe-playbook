#  Trie

A tree where each node represents a character. Paths from root spell out stored strings. Used for prefix lookup, autocomplete, dictionary, longest common prefix.

## Properties

- Each node has up to `k` children (k = alphabet size, 26 for lowercase).
- A node is marked `end` if some inserted word terminates there.
- Insert / search / delete / prefix-search: `O(L)` where `L` = word length.
- Space: `O(N * L * k)` worst case (N = number of words).

## Node

```python
class TrieNode:
    def __init__(self):
        self.children = {}   # char -> TrieNode
        self.end = False     # True if a word ends here
```

## Trie with CRUD

```python
class Trie:
    def __init__(self):
        self.root = TrieNode()

    # CREATE / UPDATE — insert a word
    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.end = True

    # READ — exact word lookup
    def search(self, word: str) -> bool:
        node = self.root
        for ch in s:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.end

    # READ — prefix lookup
    def starts_with(self, prefix: str) -> bool:
        node = self.root
        for ch in s:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True

    # DELETE — remove a word, prune dead branches
    def delete(self, word: str) -> bool:

        def helper(node: TrieNode, word: str, index: int) -> bool:
            if index == len(word):
                if not node.end:
                    return False        # word not present
                node.end = False
                return len(node.children) == 0   # prune if leaf
            ch = word[index]
            child = node.children.get(ch)
            if child is None:
                return False
            should_prune = self.helper(child, word, index + 1)
            if should_prune:
                del node.children[ch]
                return not node.end and len(node.children) == 0
            return False


        return helper(self.root, word, 0)
        


```

## Usage

```python
t = Trie()
t.insert("apple")
t.insert("app")

t.search("apple")      # True
t.search("appl")       # False
t.starts_with("appl")  # True

t.delete("apple")
t.search("apple")      # False
t.search("app")        # True   (still present)
```

## Common problems

- [Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/) — wildcard `.`
- [Word Search II](https://leetcode.com/problems/word-search-ii/) — trie + DFS on board
- [Replace Words](https://leetcode.com/problems/replace-words/)
- [Longest Word in Dictionary](https://leetcode.com/problems/longest-word-in-dictionary/)
- [Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/) — bit trie
