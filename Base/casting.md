# static_cast
```cpp
float myFloat { 3.14f };
int i1 { (int)myFloat };
int i2 { int(myFloat) }; 
int i3 { static_cast<int>(myFloat) }; // most recommend
```