Edit this diagram at https://mermaid-js.github.io/mermaid-live-editor/#/edit
```mermaid
classDiagram
	None <|-- Example1: None
	Example1 <|-- Example2: %6CF2...A982.sha256

  class None{
    empty
  }

	class Example1{
    kind hello_world
    prev NONE
    depth 0

    key1:"my_value1"
    key2:"my_value2"
    key3:"my_value3"
    key4:%JVKH...BVZG.sha256
    key5:&29F3...E6CA.sha256
    key6:@GALD...JCCG.ed25519
	}

	class Example2{
    author @DYDG...CVE0.ed25519
    kind hello_world
    prev %6CF2...A982.sha256
    depth 1

    hello:"world"
	}
```