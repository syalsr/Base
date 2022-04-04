![[00 Начало 🏠]]


# Hello

## Hello2

^5717d2
[[example_obsidian#^5717d2|move to ##Hello2]]


# Пример работы с timeline
```timeline-labeled
[line-15, body-3, active-color-interactive-accent-hover]

date: 13th April 1969
title: Enim sit amet venenatis urna
content:
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

date: 13th April 1969
title: Enim sit amet venenatis urna
content:
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

date: 16:30<br> 29th June 2000
title: Tempor commodo ullamcorper a<br> lacus vestibulum sed arcu
content:
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.<br> Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
```


```timeline
+ 13th April 1969
+ Enim sit amet venenatis urna
+ Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

+ 16:30<br> 29th June 2000
+ Tempor commodo ullamcorper a<br> lacus vestibulum sed arcu
+ Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.<br> Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
```


```timeline-labeled
date: 13th April 1969
title: Enim sit amet venenatis urna
content:
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.

date: 16:30<br> 29th June 2000
title:
content:

date: 16:30<br> 29th June 2000
title: Tempor commodo ullamcorper a<br> lacus vestibulum sed arcu
content:
Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.<br> Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.
```

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