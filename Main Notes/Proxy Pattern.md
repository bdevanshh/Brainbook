13-06-2026  17:11

Status:

Tags: [[Tags/LLD|LLD]]

# Proxy Pattern

> The proxy pattern provides a placeholder or substitute for a real object to control access to it.

### UML Diagram

![[Pasted image 20260613174138.png]]

### Code

```go
package main

import "fmt"

type Server interface {
	HandleRequest(url, method string) (int, string)
}

type Application struct {
}

func NewApplication() *Application {
	return &Application{}
}

func (a *Application) HandleRequest(url, method string) (int, string) {
	if url == "/index.html" && method == "GET" {
		return 200, "OK"
	}

	return 404, "Not Found"
}

type Nginx struct {
	app       *Application
	rateLimit map[string]int
}

func NewNginx() *Nginx {
	return &Nginx{
		app:       NewApplication(),
		rateLimit: make(map[string]int),
	}
}

func (n *Nginx) HandleRequest(url, method string) (int, string) {
	if n.rateLimit[url] > 2 {
		return 403, "Forbidden"
	}
	n.rateLimit[url]++
	return n.app.HandleRequest(url, method)
}

func main() {
	nginx := NewNginx()
	
	code, status := nginx.HandleRequest("/index.html", "GET")
	fmt.Printf("%v %v\n", code, status)
	
	code, status = nginx.HandleRequest("/index.html", "GET")
	fmt.Printf("%v %v\n", code, status)
	
	code, status = nginx.HandleRequest("/index.html", "GET")
	fmt.Printf("%v %v\n", code, status)
	
	code, status = nginx.HandleRequest("/index.html", "GET")
	fmt.Printf("%v %v\n", code, status)
}
```





# References