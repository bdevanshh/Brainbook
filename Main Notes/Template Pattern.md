12-06-2026  19:26

Status:

Tags: [[Tags/LLD|LLD]]

# Template Pattern

> Template pattern design the skeleton of an algorithm. Template class let subclasses redefine certain steps of an algorithm without changing the algorithm structure.

### UML Diagram

![[Pasted image 20260612192708.png]]

### Code

```go
package main

import "fmt"

type Model interface {
	Train()
	Evaluate()
}

type NeuralNetwork struct {
}

func NewNeuralNetwork() *NeuralNetwork {
	return &NeuralNetwork{}
}

func (n *NeuralNetwork) Train() {
	fmt.Println("Training Neural Network.")
}

func (n *NeuralNetwork) Evaluate() {
	fmt.Println("Evaluating Neural Network.")
}

type SVM struct {
}

func NewSVM() *SVM {
	return &SVM{}
}

func (n *SVM) Train() {
	fmt.Println("Training Support Vector Machine.")
}

func (n *SVM) Evaluate() {
	fmt.Println("Evaluating Support Vector Machine.")
}

type ModelTrainer struct {
	Model
}

func NewModelTrainer(model Model) *ModelTrainer {
	return &ModelTrainer{
		Model: model,
	}
}

func (t *ModelTrainer) LoadData() {
	fmt.Println("Data loaded.")
}

func (t *ModelTrainer) PreprocessData() {
	fmt.Println("Data preprocessed.")
}

func (t *ModelTrainer) Save() {
	fmt.Println("Saved model.")
}

func (t *ModelTrainer) ExecutePipeline() {
	t.LoadData()
	t.PreprocessData()
	t.Model.Train()
	t.Model.Evaluate()
	t.Save()
}

func main() {
	nn := NewNeuralNetwork()
	nnTrainer := NewModelTrainer(nn)
	nnTrainer.ExecutePipeline()
	
	svm := NewSVM()
	svmTrainer := NewModelTrainer(svm)
	svmTrainer.ExecutePipeline()
}
```





# References