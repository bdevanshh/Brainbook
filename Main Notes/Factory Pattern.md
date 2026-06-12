05-06-2026  13:40

Status:

Tags: [[Tags/LLD|LLD]]

# Factory Pattern

## The Problem

The application code is coupled with object creation code. We can delegate object creation responsibility to separate class. 

## Solution

### Simple Factory

> A factory class that decides which concrete class to instantiate.

#### UML Diagram

![[Pasted image 20260605142838.png]]

#### Code

```go
package main

import "fmt"

type Burger interface {
	Prepare()
}

type BasicBurger struct {
}

func (b *BasicBurger) Prepare() {
	fmt.Println("Preparing basic burger..")
}

type StandardBurger struct {
}

func (b *StandardBurger) Prepare() {
	fmt.Println("Preparing standard burger..")
}

type PremiumBurger struct {
}

func (b *PremiumBurger) Prepare() {
	fmt.Println("Preparing premium burger..")
}

func createBurger(burgerType string) Burger {
	if burgerType == "basic" {
		return &BasicBurger{}
	} else if burgerType == "standard" {
		return &StandardBurger{}
	} else if burgerType == "premium" {
		return &PremiumBurger{}
	} else {
		return nil
	}
}

func main() {
	b := createBurger("basic")
	b.Prepare()
}
```

### Factory Method

> Defines an interface for creating objects but, allows subclasses to decide which class to instantiate.

#### UML Diagram

![[Pasted image 20260605144943.png]]

#### Code

```go
package main

import "fmt"

type Burger interface {
	Prepare()
}

type BasicBurger struct {
}

func (b *BasicBurger) Prepare() {
	fmt.Println("Preparing basic burger..")
}

type StandardBurger struct {
}

func (b *StandardBurger) Prepare() {
	fmt.Println("Preparing standard burger..")
}

type PremiumBurger struct {
}

func (b *PremiumBurger) Prepare() {
	fmt.Println("Preparing premium burger..")
}

type BasicWheatBurger struct {
}

func (b *BasicWheatBurger) Prepare() {
	fmt.Println("Preparing basic wheat burger..")
}

type StandardWheatBurger struct {
}

func (b *StandardWheatBurger) Prepare() {
	fmt.Println("Preparing standard wheat burger..")
}

type PremiumWheatBurger struct {
}

func (b *PremiumWheatBurger) Prepare() {
	fmt.Println("Preparing premium wheat burger..")
}

type BurgerFactory interface {
	createBurger(burgerType string)
}

type SimpleBurgerFactory struct {
}

func (bf *SimpleBurgerFactory) createBurger(burgerType string) Burger {
	if burgerType == "basic" {
		return &BasicBurger{}
	} else if burgerType == "standard" {
		return &StandardBurger{}
	} else if burgerType == "premium" {
		return &PremiumBurger{}
	} else {
		return nil
	}
}

type WheatBurgerFactory struct {
}

func (bf *WheatBurgerFactory) createBurger(burgerType string) Burger {
	if burgerType == "basic" {
		return &BasicWheatBurger{}
	} else if burgerType == "standard" {
		return &StandardWheatBurger{}
	} else if burgerType == "premium" {
		return &PremiumWheatBurger{}
	} else {
		return nil
	}
}

func main() {
	factory1 := &SimpleBurgerFactory{}
	b1 := factory1.createBurger("basic")
	b1.Prepare()
	
	factory2 := &WheatBurgerFactory{}
	b2 := factory2.createBurger("premium")
	b2.Prepare()
}
```

### Abstract Factory Method

> Provides an interface for creating families of related objects without specifying their concrete classes.

#### UML Diagram

![[Pasted image 20260605150544.png]]

#### Code

```go
package main

import "fmt"

type Burger interface {
	Prepare()
}

type BasicBurger struct {
}

func (b *BasicBurger) Prepare() {
	fmt.Println("Preparing basic burger..")
}

type BasicWheatBurger struct {
}

func (b *BasicWheatBurger) Prepare() {
	fmt.Println("Preparing basic wheat burger..")
}

type GarlicBread interface {
	Prepare()
}

type BasicGarlicBread struct {
}

func (b *BasicGarlicBread) Prepare() {
	fmt.Println("Preparing basic garlic bread..")
}

type BasicWheatGarlicBread struct {
}

func (b *BasicWheatGarlicBread) Prepare() {
	fmt.Println("Preparing basic wheat garlic bread..")
}

type MealFactory interface {
	createBurger(burgerType string) Burger
	createGarlicBread(garlicBreadType string) GarlicBread
}

type SimpleMealFactory struct {
}

func (mf *SimpleMealFactory) createBurger(burgerType string) Burger {
	if burgerType == "basic" {
		return &BasicBurger{}
	} else {
		return nil
	}
}

func (mf *SimpleMealFactory) createGarlicBread(garlicBreadType string) GarlicBread {
	if garlicBreadType == "basic" {
		return &BasicGarlicBread{}
	} else {
		return nil
	}
}

type WheatMealFactory struct {
}

func (mf *WheatMealFactory) createBurger(burgerType string) Burger {
	if burgerType == "basic" {
		return &BasicWheatBurger{}
	} else {
		return nil
	}
}

func (mf *WheatMealFactory) createGarlicBread(garlicBreadType string) GarlicBread {
	if garlicBreadType == "basic" {
		return &BasicWheatGarlicBread{}
	} else {
		return nil
	}
}

func main() {
	factory1 := &SimpleMealFactory{}
	b1 := factory1.createBurger("basic")
	b1.Prepare()
	
	g1 := factory1.createGarlicBread("basic")
	g1.Prepare()
	
	factory2 := &WheatMealFactory{}
	b2 := factory2.createBurger("basic")
	b2.Prepare()
	
	g2 := factory2.createGarlicBread("basic")
	g2.Prepare()
}
```





# References