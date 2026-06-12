03-06-2026  14:51

Status:

Tags: [[Tags/LLD|LLD]]

# SOLID

## S: Single Responsibility Principal (SRP)

> A class should have only one reason to change.
> A class should do only one thing.

### Wrong

![[Pasted image 20260603150429.png]]

Shopping cart has 3 responsibility. Everytime we need to change how we store the data in DB, we need to change the class. Same with all other methods.
### Correct

![[Pasted image 20260603151101.png]]

## O: Open-Close Principal (OCP)

> A class should be open for extension, but close for modification.

### Wrong

![[Pasted image 20260603152034.png]]

CardDBStorage class needs to be changed for adding multiple storage destinations.

### Correct

![[Pasted image 20260603153937.png]]

## L: Liskov Substitution Principal (LSP)

> Subclasses should be substitutable for their base classes.

### Guidelines for Substitution

#### Signature Rule

##### Method Arguments Rule
When method overriding, child class methods should take exactly same or broader types than parent class methods in arguments.

```cpp
class Parent {
public:
	virtual void print(string msg) {
	...
	}
}

class Child: public Parent {
	void print(string msg) override {
	...
	}
}
```
##### Return Type Rule
When method overriding, child class methods should return same or narrow types than parent class methods return type.

```cpp
class Parent {
public:
	virtual Animal* print() {
	...
	}
}

class Child: public Parent {
	Dog* print() override {
	...
	}
}
```
##### Exception Rule

When method overriding, child class methods should throw same or narrow type exception than parent class methods.

```cpp
class Parent {
public:
	virtual void print() noexcept(false) {
		throw logic_error("Parent error");
	}
}

class Child: public Parent {
	void print() noexcept(false) override {
		throw out_of_range("Child error");
	}
}
```

#### Property Rule

##### Class Invariant
Child class should follow or strengthen the parent class's invariant(rules), not weaken it.

```cpp
// Invariant: Balance always positive
class Account {
public:
	void withdraw(int amount) {
		if(balance >= amount) {
			balance -= amount;
		}
	}
}

// Breaks the invariant
class CheatAccount : public Account {
	void withdraw(int amount) {
		balance -= amount;
	}
}
```
##### History Constraint
Child class should follow parent class's history constraints.

```cpp
// History Constraint: Withdraw should always be used
class Account {
public:
	virtual void withdraw(int amount) {
	..
	}
}

// Breaks the history constraint
class CheatAccount : public Account {
	void withdraw(int amount) {
		throw logic_error("Can't use withdraw");
	}
}
```

#### Method Rule

##### Precondition Rule
Child class should follow or weaken the parent class's preconditions, not strengthen it.

```cpp
class User {
	void createPassword(); // Precondition: length >= 8
};

class AdminUser: public User {
	void createPassword(); // Precondition: length >= 6
};
```

##### Postcondition Rule
Child class should follow or strengthen the parent class's postconditions, not weaken it.

```cpp
class Car {
	void brake(); // Postcondition: Reduce the speed
};

class ElectricCar: public Car {
	void brake(); // Postcondition: Reduce the speed similarly or more
};
```

### Wrong

![[Pasted image 20260603155611.png]]

This is fails LSP because, the FixedDepositAccount can't substitute for the Account's withdraw method.
### Correct

![[Pasted image 20260603160605.png]]

## I: Interface Segregation Principal (ISP)

> Many client specific interface are better than one general purpose interface.
> Client should not be forced to implement methods they don't need.

### Wrong

![[Pasted image 20260603172129.png]]

2D shapes doesn't need `volume()` methods, which is forced by single general interface.
### Correct

![[Pasted image 20260603172410.png]]

## D: Dependency Inversion Principal (DIP)

> High level module should not depend on low level module, but rather both should depend on abstraction.

### Wrong

![[Pasted image 20260603173419.png]]

Application directly depends on DB classes. When you want to add new DB, you need to change the Application class, which breaks the Open-Close Principal.
### Correct

![[Pasted image 20260603174044.png]]





# References