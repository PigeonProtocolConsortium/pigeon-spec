Edit this diagram at https://mermaid-js.github.io/mermaid-live-editor/#/edit
```mermaid
classDiagram
	None <|-- Example1: None
	Example1 <|-- Example2: %6CF2...A982.sha256

  class None{
    empty
  }

	class Example1{
    author @78V...64G.ed25519
    kind b04...b2e
    prev NONE
    depth 0
    lipmaa 0

    cool_message:%KDK...WKG.sha256

    signature X4K...E10.sig.ed25519
	}

	class Example2{
    author @78V...64G.ed25519
    kind ba1...6e8
    prev %KDK...WKG.sha256
    depth 1
    lipmaa 1

    hello:"World"
    this_is_a:"Key"

    signature JSS...W3G.sig.ed25519
	}
```