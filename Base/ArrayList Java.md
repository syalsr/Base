# ArrayList Java
Динамический массив.

```java
ArrayList<Integer> arr = new ArrayList<>();
for(int i = 0;  i < 100; ++i)
{
	arr.add(i);
}
System.out.println(arr);
System.out.println(arr.get(0));//0
System.out.println(arr.get(99));//99
System.out.println(arr.size());//100
arr.remove(5);//index - 5
```