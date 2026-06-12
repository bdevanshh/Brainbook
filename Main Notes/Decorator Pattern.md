07-06-2026  17:13

Status:

Tags: [[Tags/LLD|LLD]]

# Decorator Pattern

> It attaches additional responsibilities to an object dynamically. It provides flexible alternative to subclassing for extending functionality.

### UML Diagram

![[Pasted image 20260607172818.png]]

### Code

```go
package main

import "fmt"

type ICharacter interface {
	GetAbilities() string
}

type Mario struct{}

func (m *Mario) GetAbilities() string {
	return "Mario"
}

func NewMario() *Mario {
	return &Mario{}
}

type HeightUp struct {
	character ICharacter
}

func NewHeightUp(character ICharacter) *HeightUp {
	return &HeightUp{
		character: character,
	}
}

func (h *HeightUp) GetAbilities() string {
	return h.character.GetAbilities() + " Height UP"
}

type GunPowerup struct {
	character ICharacter
}

func NewGunPowerup(character ICharacter) *GunPowerup {
	return &GunPowerup{
		character: character,
	}
}

func (g *GunPowerup) GetAbilities() string {
	return g.character.GetAbilities() + " Gun Powerup"
}

type StarPowerup struct {
	character ICharacter
}

func NewStarPowerup(character ICharacter) *StarPowerup {
	return &StarPowerup{
		character: character,
	}
}

func (s *StarPowerup) GetAbilities() string {
	return s.character.GetAbilities() + " Star Powerup"
}

func main() {
	var c ICharacter = NewMario()
	fmt.Println(c.GetAbilities())
	
	c = NewHeightUp(c)
	fmt.Println(c.GetAbilities())
	
	c = NewGunPowerup(c)
	fmt.Println(c.GetAbilities())
	
	c = NewStarPowerup(c)
	fmt.Println(c.GetAbilities())
}
```





# References