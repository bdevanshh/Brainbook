10-06-2026  18:47

Status:

Tags: [[Tags/LLD|LLD]]

# Facade Pattern

> Facade provides a simplified, unified interface to a set of complex subsystem. It hides the complexity of the subsystem and exposes only what is necessary.

### UML Diagram

![[Pasted image 20260610190621.png]]

### Code

```go
package main

import "fmt"

type CPU struct{}

func NewCPU() *CPU {
	return &CPU{}
}

func (c *CPU) initialize() {
	fmt.Println("Initializing CPU.")
}

type PowerSupply struct{}

func NewPowerSupply() *PowerSupply {
	return &PowerSupply{}
}

func (p *PowerSupply) power() {
	fmt.Println("Starting power supply.")
}

type BIOS struct {
	CPU    *CPU
	Memory *Memory
}

func NewBIOS(cpu *CPU, memory *Memory) *BIOS {
	return &BIOS{
		CPU:    cpu,
		Memory: memory,
	}
}

func (b *BIOS) boot() {
	fmt.Println("Booting up BIOS.")
}

type Memory struct{}

func NewMemory() *Memory {
	return &Memory{}
}

func (m *Memory) SelfTest() {
	fmt.Println("Self testing memory.")
}

type OperatingSystem struct{}

func NewOperatingSystem() *OperatingSystem {
	return &OperatingSystem{}
}

func (o *OperatingSystem) Load() {
	fmt.Println("Loading the operating system.")
}

type ComputerFacade struct {
	Cpu             *CPU
	PowerSupply     *PowerSupply
	Bios            *BIOS
	Memory          *Memory
	OperatingSystem *OperatingSystem
}

func NewComputerFacade() *ComputerFacade {
	facade := &ComputerFacade{
		Cpu:             NewCPU(),
		PowerSupply:     NewPowerSupply(),
		Memory:          NewMemory(),
		OperatingSystem: NewOperatingSystem(),
	}

	facade.Bios = NewBIOS(facade.Cpu, facade.Memory)

	return facade
}

func (f *ComputerFacade) StartComputer() {
	f.PowerSupply.power()
	f.Cpu.initialize()
	f.Memory.SelfTest()
	f.Bios.boot()
	f.OperatingSystem.Load()
}

func main() {
	facade := NewComputerFacade()
	facade.StartComputer()
}
```





# References