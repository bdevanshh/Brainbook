12-06-2026  18:16

Status:

Tags: [[Tags/LLD|LLD]]

# Composite Pattern

> It composes object into tree like structure. It lets the client treat individual object and composition of object uniformly.

### UML Diagram

![[Pasted image 20260612183624.png]]

### Code

```go
package main

import (
	"fmt"
	"strings"
)

type FileSystemItem interface {
	LS()
	OpenAll(indent int)
	GetSize() int
	CD(name string) FileSystemItem
	GetName() string
	IsFolder() bool
}

type File struct {
	Name string
	Size int
}

func NewFile(name string, size int) *File {
	return &File{
		Name: name,
		Size: size,
	}
}

func (f *File) LS() {
	fmt.Println(f.Name)
}

func (f *File) OpenAll(indent int) {
	fmt.Println(strings.Repeat(" ", indent), f.Name)
}

func (f *File) GetSize() int {
	return f.Size
}

func (f *File) CD(name string) FileSystemItem {
	return nil
}

func (f *File) GetName() string {
	return f.Name
}

func (f *File) IsFolder() bool {
	return false
}

type Folder struct {
	Name   string
	Childs []FileSystemItem
}

func NewFolder(name string) *Folder {
	return &Folder{
		Name:   name,
		Childs: []FileSystemItem{},
	}
}

func (f *Folder) Add(item FileSystemItem) {
	f.Childs = append(f.Childs, item)
}

func (f *Folder) LS() {
	fmt.Println(f.Name)
	
	for _, item := range f.Childs {
		fmt.Printf("[ %s\n", item.GetName())
	}
}

func (f *Folder) OpenAll(indent int) {
	fmt.Println(strings.Repeat(" ", indent), f.Name)
	
	for _, item := range f.Childs {
		item.OpenAll(indent + 4)
	}
}

func (f *Folder) GetSize() int {
	size := 0
	for _, item := range f.Childs {
		size += item.GetSize()
	}

	return size
}

func (f *Folder) CD(name string) FileSystemItem {
	for _, item := range f.Childs {
		if item.GetName() == name && item.IsFolder() {
			return item
		}
	}
	
	return nil
}

func (f *Folder) GetName() string {
	return f.Name
}

func (f *Folder) IsFolder() bool {
	return true
}

func main() {
	root := NewFolder("/")
	root.Add(NewFolder("dev"))
	
	user := NewFolder("user")
	user.Add(NewFile(".zshrc", 10))
	
	home := NewFolder("home")
	home.Add(user)
	
	root.Add(home)
	
	root.Add(NewFolder("mnt"))
	root.Add(NewFile("tmp.txt", 3))
	
	root.OpenAll(0)
	
	// root.LS()
	
	// cwd := root.CD("home")
	// cwd.LS()
	// cwd = cwd.CD("user")
	// cwd.LS()
	
	// fmt.Println(root.GetSize())
}
```





# References