15-08-2025  16:40

Status: #Revision-04

Tags: [[Tags/DSA]] [[Stack]]

# LRU Cache

https://leetcode.com/problems/lru-cache/description/

- Create a doubly linked list and a map(key, node).
- For get function,
	- Check if the key is in the map.
	- Return the value, if exist and move the node to the start. 
- For put function,
	- If key is in the map, update the node's value and move it to the start.
	- If the capacity is reached, delete last node.
	- Create new node and add it to start and in the map.

```cpp
struct Node {
    Node* prev;
    Node* next;
    int key;
    int value;
	
    Node(Node* prev, Node* next, int key, int value) {
        this->prev = prev;
        this->next = next;
        this->key = key;
        this->value = value;
    }
};

class DoubleyLinkedList {
public:
    Node* head;
    Node* tail;
	
    DoubleyLinkedList() {
        tail = new Node(NULL, NULL, -1, -1);
        head = new Node(NULL, tail, -1, -1);
		
        tail->prev = head;
    }
	
    void insertAfterHead(Node* node) {
        node->prev = head;
        node->next = head->next;
        
        head->next->prev = node;
        head->next = node;
    }
	
    void deleteNode(Node* node) {
        node->prev->next = node->next;
        node->next->prev = node->prev;
		
        node->next = NULL;
        node->prev = NULL;
    }
};

class LRUCache {
public:
    DoubleyLinkedList* dll;
    map<int, Node*> mp;
    int capacity;
    
    LRUCache(int capacity) {
        dll = new DoubleyLinkedList();
        this->capacity = capacity;    
    }
    
    // O(1)
    int get(int key) {
        if(mp.find(key) == mp.end()) {
            return -1;
        }
		
        Node* node = mp[key];
		
        dll->deleteNode(node);
        dll->insertAfterHead(node);
		
        return node->value;
    }
    
    // O(1)
    void put(int key, int value) {
        if(mp.find(key) != mp.end()) {
            Node* node = mp[key];
			
            node->value = value;
			
            dll->deleteNode(node);
            dll->insertAfterHead(node);
            return;
        }
		
        if(mp.size() == capacity) {
	        Node* lru = dll->tail->prev;
			
            dll->deleteNode(lru);
            mp.erase(lru->key); // IMPORTANT
			
            delete lru;
        }
		
        Node* node = new Node(NULL, NULL, key, value);
        dll->insertAfterHead(node);
		
        mp[key] = node;
    }
};
```


|                              **Time Complexity**                               |
| :----------------------------------------------------------------------------: |
| $O(\log n)$ (Map), <br><br>$O(n)$ (Unordered Map)<br>$O(n^2)$ (Worst Case)<br> |
For N function calls,




# References