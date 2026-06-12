07-06-2026  16:19

Status:

Tags: [[Tags/LLD|LLD]]

# Observer Pattern

> Defines 1-to-M relationship between objects so that when one object changes state, all of its dependants are notified, and updated automatically.

### UML Diagram

![[Pasted image 20260607163825.png]]

### Code

```go
package main

import (
	"fmt"
	"slices"
)

type ISubscriber interface {
	Update()
}

type Subscriber struct {
	name    string
	channel *Channel
}

func NewSubscriber(name string, channel *Channel) *Subscriber {
	return &Subscriber{
		name:    name,
		channel: channel,
	}
}

func (s *Subscriber) Update() {
	fmt.Printf("%s uploaded a new video with title %s\n", s.channel.name, s.channel.latestVideo)
}

type IChannel interface {
	Subscribe(subscriber ISubscriber)
	Unsubscribe(subscriber ISubscriber)
	Notify()
}

type Channel struct {
	name        string
	latestVideo string
	subscribers []ISubscriber
}

func NewChannel(name string) *Channel {
	return &Channel{
		name:        name,
		subscribers: []ISubscriber{},
	}
}

func (c *Channel) Subscribe(subscriber ISubscriber) {
	c.subscribers = append(c.subscribers, subscriber)
}

func (c *Channel) Unsubscribe(s ISubscriber) {
	c.subscribers = slices.DeleteFunc(c.subscribers, func(subscriber ISubscriber) bool {
		return subscriber == s
	})
}

func (c *Channel) Notify() {
	for _, subscriber := range c.subscribers {
		subscriber.Update()
	}
}

func (c *Channel) UploadVideo(title string) {
	c.latestVideo = title
	c.Notify()
}

func main() {
	c := NewChannel("3Blue1Brown")
	
	s1 := NewSubscriber("Clux", c)
	s2 := NewSubscriber("James", c)
	
	c.Subscribe(s1)
	c.Subscribe(s2)
	
	c.UploadVideo("How LLMs work!")
	
	c.Unsubscribe(s1)
	
	c.UploadVideo("Understanding trigonometry")
}
```





# References