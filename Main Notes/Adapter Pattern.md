10-06-2026  18:18

Status:

Tags: [[Tags/LLD|LLD]]

# Adapter Pattern

> Adapter converts the interface of a class (Adaptee) into another interface that client expects.

### UML Diagram

![[Pasted image 20260610184405.png]]

### Code

```go
package main

import (
	"fmt"
	"strings"
)

type IReport interface {
	GetJsonData() string
}

type XMLDataProvider struct{}

func NewXMLDataProvider() *XMLDataProvider {
	return &XMLDataProvider{}
}

func (p *XMLDataProvider) GetXMLData() string {
	return "<xml><user><name>Clux</name></user></xml>"
}

type XMLDataProviderAdapter struct {
	provider *XMLDataProvider
}

func NewXMLDataProviderAdapter(provider *XMLDataProvider) *XMLDataProviderAdapter {
	return &XMLDataProviderAdapter{
		provider: provider,
	}
}

func (a *XMLDataProviderAdapter) GetJsonData() string {
	xmlData := a.provider.GetXMLData()
	xmlData = strings.TrimPrefix(xmlData, "<xml><user><name>")
	xmlData = strings.TrimSuffix(xmlData, "</name></user></xml>")
	
	return "{'name': '" + xmlData + "'}"
}

type Client struct {
	report IReport
}

func NewClient(report IReport) *Client {
	return &Client{
		report: report,
	}
}

func (c *Client) GetData() {
	fmt.Println(c.report.GetJsonData())
}

func main() {
	xmlProvider := NewXMLDataProvider()
	xmlProviderAdapter := NewXMLDataProviderAdapter(xmlProvider)
	client := NewClient(xmlProviderAdapter)
	
	client.GetData()
}
```





# References