05-06-2026  12:35

Status:

Tags: [[Tags/LLD|LLD]]

# Strategy Pattern

## The Problem

We have a robot class that have talk, walk and fly methods but, not all robots can do all of these things. So, we need to segregate the interface for each of the method and handle all combination of these methods.

The class hierarchy will look something like this:

![[Pasted image 20260605123536.png]]

## Solution

> Define family of algorithms, put them into separate classes so that they can be changed at runtime.

![[Pasted image 20260605125253.png]]

### UML Diagram

![[Pasted image 20260605135825.png]]

### Code

```go
package main

import "fmt"

type Talkable interface {
	Talk()
}

type NormalTalk struct {
}

func NewNoramlTalk() *NormalTalk {
	return &NormalTalk{}
}

func (t *NormalTalk) Talk() {
	fmt.Println("Normal talk.")
}

type NoTalk struct {
}

func NewNoTalk() *NoTalk {
	return &NoTalk{}
}

func (t *NoTalk) Talk() {
	fmt.Println("No talk.")
}

type Walkable interface {
	Walk()
}

type NormalWalk struct {
}

func NewNormalWalk() *NormalWalk {
	return &NormalWalk{}
}

func (w *NormalWalk) Walk() {
	fmt.Println("Normal Walk.")
}

type NoWalk struct {
}

func NewNoWalk() *NoWalk {
	return &NoWalk{}
}

func (w *NoWalk) Walk() {
	fmt.Println("No Walk.")
}

type Flyable interface {
	Fly()
}

type NormalFly struct {
}

func NewNormalFly() *NormalFly {
	return &NormalFly{}
}

func (f *NormalFly) Fly() {
	fmt.Println("Normal Fly.")
}

type NoFly struct {
}

func NewNoFly() *NoFly {
	return &NoFly{}
}

func (f *NoFly) Fly() {
	fmt.Println("No Fly.")
}

type Robot struct {
	t Talkable
	w Walkable
	f Flyable
}

func (r *Robot) Talk() {
	r.t.Talk()
}

func (r *Robot) Walk() {
	r.w.Walk()
}

func (r *Robot) Fly() {
	r.f.Fly()
}

type CompanionRobot struct {
	Robot
}

func NewCompanionRobot(t Talkable, w Walkable, f Flyable) *CompanionRobot {
	return &CompanionRobot{
		Robot{
			t,
			w,
			f,
		},
	}
}

func (r *CompanionRobot) Projection() {
	fmt.Println("Displaying companion robot")
}

type WorkerRobot struct {
	Robot
}

func NewWorkerRobot(t Talkable, w Walkable, f Flyable) *WorkerRobot {
	return &WorkerRobot{
		Robot{
			t,
			w,
			f,
		},
	}
}

func (r *WorkerRobot) Projection() {
	fmt.Println("Displaying worker robot")
}

func main() {
	cr := NewCompanionRobot(NewNoramlTalk(), NewNormalWalk(), NewNoFly())
	cr.Fly()
	cr.Walk()
	cr.Talk()
	cr.Projection()
	
	wr := NewWorkerRobot(NewNoTalk(), NewNoWalk(), NewNormalFly())
	wr.Fly()
	wr.Walk()
	wr.Talk()
	wr.Projection()
}
```





# References