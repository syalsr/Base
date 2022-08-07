# HashMap Java
Реализуется [[Hash tables]] - неотсортированный словарь, если важен порядок, то нужно использовать [[LinkedHashMap Java]] или [[TreeMap Java]]
```java
HashMap<Integer, String> map = new HashMap<>();  
map.put(1, "one");  
map.put(2, "two");
map.put(2, "twooo");//происходит перезапись
System.out.println(map.get(2));//2 twoo
  
for(HashMap.Entry<Integer, String> entry : map.entrySet())  //порядок вывода не гарантирован
{  
    System.out.println(entry.getKey() + " " + entry.getValue());  
}
```