# JUnit
Отличии assert и assume в том, что если тест завален assert сообщает об этом, а assume игнорирует это 
#do/start 
1. mockito
2. детальнее разобраться со всем базоовым, найти статью может какую-то
# Аннотации
```
@BeforeClass - запускается только один раз при запуске теста(static метод)
@Before - запускается перед каждым тестовым методом
@After - запускается после каждого метода
@AfterClass - запускается после того, как отработали все тестовые методы(static метод)
```

Допустим, если мы тестируем БД, то Before/After class будет вызываться один раз поскольку только один раз нужно установить/разорвать соединение с БД, заполнить данными для всех тестов.


# Пример
```java
public class Vector {  
    private double x;  
    private double y;  
  
    public double getY() {  
        return y;  
    }  
  
    public void setY(double y) {  
        this.y = y;  
    }  
  
    public double getX() {  
        return x;  
    }  
  
    public void setX(double x) {  
        this.x = x;  
    }  
  
    public double length(){  
        return Math.sqrt(x * x + y * y);  
    }  
    public double calc(double k){  
        if(k == 0)  
            throw new ArithmeticException("Cant divide by zero");  
        return x / k + y / k;  
    }  
}

public class VectorTest {  
    private final double EPS = 1e-9;  
    private static Vector v;  
  
    @BeforeClass//либо можем использовать @Before, чтобы создавать заново новый вектор перед каждым тестом.  
    public static void createNewVector(){  
        v = new Vector();  
    }  
  
    @Test  
    public void newVectorShouldHaveZeroLength(){  
        Assert.assertEquals(0, v.length(), EPS);  
        //1e-9 - delta, т.е. числа могут различаться на  
        //0.0000000001    }  
    @Test(expected = ArithmeticException.class)  
    public void zeroPassedVarShouldThrowException(){  
        v.calc(0);  
    }  
    @Test(timeout = 1000)  
    public void speedCalcShouldReturnFasterThanOneMillesecond(){  
        v.calc(2);  
    }  
}
```