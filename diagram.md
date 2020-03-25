Edit this diagram at https://mermaid-js.github.io/mermaid-live-editor/#/edit
```mermaid
classDiagram
	None <|-- Example1: None
	Example1 <|-- Example2: @DYdgK...Cve04=.ed25519

  class None{
    empty
  }

	class Example1{
    author @DYdgK...Cve04=.ed25519
    kind hello_world
    prev NONE
    depth 0

    hello:"world"
	}

	class Example2{
    kind hello_world
    prev NONE
    depth 0

    key1:"my_value\n"
    key2:"my_value2"
    key3:"my_value3"
    key4:%jvKh9...bvzGM=.sha256
    key5:&29f39...e6ca3.sha256
    key6:@galda...WJccg=.ed25519
	}
```