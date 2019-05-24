# java回调机制
## 回调的基本思想
* ### 类A的a()方法调用类B的b()方法
* ### 类B的b()方法执行完毕主动调用类A的callback()方法

#### 看一个场景，老师布置计算题给小明，小明不会，刚好小红有一个超级计算器，所以小明向小红请教，小红计算这道题也需要时间，如果在这个过程中，小明一直在等结果，就属于“同步回调”，如果小明去干别的事了，结果出来后在来看结果就属于“异步回调”，当然小红这种计算能力还可以做别的事啊，这是有个商贩老婆婆找小红做小帮手。我们直接可以想到的是通过继承来实现。
#### 实际使用中，考虑到java的单继承，以及不希望把自身太多东西暴漏给别人，这里使用从接口继承的方式配合内部类来做。

#### 换句话说，小红希望以后继续向班里的小朋友们提供计算服务，同时还能向老婆婆提供算账服务，甚至以后能够拓展其他人的业务，于是她向所有的顾客约定了一个办法，用于统一的处理，也就是自己需要的操作数和做完计算之后应该怎么做。这个统一的方法，小红做成了一个接口，提供给了大家，代码如下：
```
public interface doJob
{
    public void fillBlank(int a, int b, int result);
}
```
#### 因为灵感来自帮小明填空，因此小红保留了初心，把所有业务都当做填空（fillBlank）来做。

#### 同时，小红修改了自己的计算器，使其可以同时处理不同的实现了doJob接口的人，代码如下
```
public class SuperCalculator
{
    public void add(int a, int b, doJob  customer)
    {
        int result = a + b;
        customer.fillBlank(a, b, result);
    }
}
```
#### 小明和老婆婆拿到这个接口之后，只要实现了这个接口，就相当于按照统一的模式告诉小红得到结果之后的处理办法，按照之前说的使用内部类来做，代码如下：

#### 小明的：
```
public class Student
{
    private String name = null;

    public Student(String name)
    {
        // TODO Auto-generated constructor stub
        this.name = name;
    }
    
    public void setName(String name)
    {
        this.name = name;
    }
    
    public class doHomeWork implements doJob
    {

        @Override
        public void fillBlank(int a, int b, int result)
        {
            // TODO Auto-generated method stub
            System.out.println(name + "求助小红计算:" + a + " + " + b + " = " + result);
        }
        
    }
    
    public void callHelp (int a, int b)
    {
        new SuperCalculator().add(a, b, new doHomeWork());
    }
}
```
#### 老婆婆的：
```
public class Seller
{
    private String name = null;

    public Seller(String name)
    {
        this.name = name;
    }
    
    public void setName(String name)
    {
        this.name = name;
    }
    
    public class doHomeWork implements doJob
    {

        @Override
        public void fillBlank(int a, int b, int result)
        {
            System.out.println(name + "求助小红算账:" + a + =" + " + b + " = " + result + "元");
        }
        
    }
    
    public void callHelp (int a, int b)
    {
        new SuperCalculator().add(a, b, new doHomeWork());
    }
}
```
#### 测试程序：
```
public class Test
{
    public static void main(String[] args)
    {
        int a = 56;
        int b = 31;
        int c = 26497;
        int d = 11256;
        Student s1 = new Student("小明");
        Seller s2 = new Seller("老婆婆");
        
        s1.callHelp(a, b);
        s2.callHelp(c, d);
    }
}
```
#### 运行结果如下：
```
小明求助小红计算:56 + 31 = 87
老婆婆求助小红算账:26497 + 11256 = 37753元
```

### 这就是回调机制，灵活运用接口比继承多态的代码可读性要高的多，并且方便修改。