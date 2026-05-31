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

- Implement Trie (LC 208)
- Add and Search Word — wildcard `.` (LC 211)
- Word Search II — trie + DFS on board (LC 212)
- Replace Words (LC 648)
- Longest Word in Dictionary (LC 720)
- Maximum XOR of Two Numbers in Array — bit trie (LC 421)
