# Reflection API
С помощью него мы можем получать данные о классах.
```java
public class Main {  
    public static void main(String[] args) {  
        User user = new User(25L, "Ivan");  
        Class<? extends User> userClass = user.getClass();  
        Class<User> userClass1 = User.class;  
        System.out.println(userClass == userClass1);  
    }  
  
    private class Test1 {  
  
    }  
  
    private static class Test3 {  
  
    }  
  
    private enum Test2 {  
        ONE, TWO  
    }  
    static class User extends Person implements Serializable, Comparable<User> {  
  
        private String name;  
  
        public User(Long id, String name) {  
            super(id);  
            this.name = name;  
        }  
  
        @Override  
        public int compareTo(User o) {  
            return 0;  
        }  
  
        public String getName() {  
            return name;  
        }  
  
        public void setName(String name) {  
            this.name = name;  
        }  
  
        @Override  
        public String toString() {  
            return "User{" +  
                    "name='" + name + '\'' +  
                    "} " + super.toString();  
        }  
    }  
    static abstract class Person {  
  
        private Long id;  
  
        public Person(Long id) {  
            this.id = id;  
        }  
  
        public Long getId() {  
            return id;  
        }  
    }  
}
```