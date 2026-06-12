06-06-2026  17:12

Status:

Tags: [[Tags/LLD|LLD]]

# Singleton Pattern

> Allows only a single object of the class to be created.

## Code

```go
package main

import (
	"fmt"
	"sync"
)

var mu sync.Mutex

type Singleton struct {
}

var instance *Singleton

func NewSingleton() *Singleton {
	if instance == nil {
		// Lock as later as possible
		mu.Lock()
		defer mu.Unlock()
		
		if instance == nil {
			fmt.Println("Created new singleton.")
			instance = &Singleton{}
		}
	}
	
	return instance
}

func main() {
	for i := 0; i < 10; i++ {
		go NewSingleton()
	}
	
	fmt.Scanln()
}
```





# References