# UML
```plantuml
class SpreadsheetCell

SpreadsheetCell <|-- ArrayList
SpreadsheetCell <|-- ArrayList1
```

```plantuml
interface SpreadsheetCell

SpreadsheetCell <|- ArrayList
SpreadsheetCell <|- ArrayList1
```

```plantuml
abstract class SpreadsheetCell
{
	void test();
	void test();
	int a;
	int b;
}
note left: note left test
class ArrayList
{
	void test();
}
note right: note left test

class ArrayList1
{
	void test();
}
note right: note left test

SpreadsheetCell <|--- ArrayList
SpreadsheetCell <|-- ArrayList1
SpreadsheetCell <|- ArrayList2
```
