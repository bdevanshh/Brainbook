10-06-2026  17:45

Status:

Tags: [[Tags/LLD|LLD]]

# Command Pattern

> Encapsulate a request as an object, letting you parameterize clients with different request and support undo-able operations.

### UML Diagram

![[Pasted image 20260610175825.png]]

### Code

```go
package main

import "fmt"

type ICommand interface {
	Execute()
	Undo()
}

type Light struct {
}

func NewLight() *Light {
	return &Light{}
}

func (l *Light) On() {
	fmt.Println("Light turned On!")
}

func (l *Light) Off() {
	fmt.Println("Light turned Off!")
}

type LightCommand struct {
	Light *Light
}

func NewLightCommand(light *Light) *LightCommand {
	return &LightCommand{
		Light: light,
	}
}

func (c *LightCommand) Execute() {
	c.Light.On()
}

func (c *LightCommand) Undo() {
	c.Light.Off()
}

const buttonCount = 4

type Remote struct {
	commands     [buttonCount]ICommand
	commandState [buttonCount]bool
}

func NewRemote() *Remote {
	return &Remote{
		commands:     [buttonCount]ICommand{},
		commandState: [buttonCount]bool{},
	}
}

func (r *Remote) SetCommand(idx int, command ICommand) {
	r.commands[idx] = command
	r.commandState[idx] = false
}

func (r *Remote) PressButton(idx int) {
	if r.commandState[idx] {
		r.commands[idx].Undo()
	} else {
		r.commands[idx].Execute()
	}
	
	r.commandState[idx] = !r.commandState[idx]
}

func main() {
	light := NewLight()
	
	remote := NewRemote()
	remote.SetCommand(0, NewLightCommand(light))
	
	remote.PressButton(0)
	remote.PressButton(0)
	remote.PressButton(0)
}
```





# References