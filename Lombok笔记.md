# Lombok

####1、省略POJO的常用方法 ：@Data @ToString @Constructor...

####2、局部变量类型推断：val 和 var

 `Java10+`已经支持通过查看等号右侧的表达式来推断局部变量类型，在之前的版本中可以通过 `Lombok`的``val``和``var``类型实现；

`val`在背后创建了一个 `final`且不可变的变量，

`var`在背后创建了一个可变本地变量。

~~~java
final Map<String, Integer> map = new HashMap();
map.put("joe", 21);

Lombok----------------------
    
val valMap = new HashMap<String, Integer>();
map.put("joe", 21);
~~~

####3、@NonNull 非NULL判断

~~~java
public void nonNullDemo( Employee employee, Account account){
    if(employee == null){
        throw new IllegalArgumentException("Employee is marked @NonNull but is null");
    }
    if(account == null){
        throw new IllegalArgumentException("Account is marked @NonNull but is null");
    }
   
    doSomething....
}


Lombok----------------------
   
    public void nonNullDemo( @NotNull Employee employee, @NotNull Account account){
    
    doSomething....
}
~~~

默认情况下， `Lombok`会抛出 `NullPointerException`，也可以配置 `Lombok`抛出 `IllegalArgumentException`

~~~properties
# 指定抛出异常类型，当然也不是什么类型的异常都可以使用的（tips:希望后期可以支持自定义异常）
# 默认使用的是 NullPointerException
lombok.nonNull.exceptionType = [NullPointerException | IllegalArgumentException] (default: NullPointerException).
# 是否允许使用 @NonNull 注解
lombok.nonNull.flagUsage = [warning | error] (default: not set)
~~~

####4、@Cleanup

使用`@Cleanup`来注释任何**局部变量**声明确保在代码执行后，在退出当前作用域之前自动清除给定资源。

~~~java
@Cleanup InputStream in = new FileInputStream（“some/file”）;
~~~

示例：

~~~java
public class CleanUpTest {
    public static void main(String[] args) throws IOException {
        File file = new File("fileNameScr");
        File file1 = new File("fileNameDest");
        @Cleanup InputStream inputStream = new FileInputStream(file);
        @Cleanup OutputStream outputStream = new FileOutputStream(file1);
        byte[] bytes = new byte[1024];
        int len = -1;
        while (true) {
            len = inputStream.read(bytes);
            if (len == -1) {
                break;
            }
            outputStream.write(bytes,0,len);
        }
    }
}
~~~

编译后

~~~java
public class CleanUpTest {
    public static void main(String[] args) throws IOException {
        File file = new File("fileNameScr");
        File file1 = new File("fileNameDest");
        FileInputStream inputStream = new FileInputStream(file);

        try {
            FileOutputStream outputStream = new FileOutputStream(file1);
            try {
                byte[] bytes = new byte[1024];
                boolean var6 = true;
                while(true) {
                    int len = inputStream.read(bytes);
                    if (len == -1) {
                        return;
                    }
                    outputStream.write(bytes, 0, len);
                }
            } finally {
                if (Collections.singletonList(outputStream).get(0) != null) {
                    outputStream.close();
                }
            }
        } finally {
            if (Collections.singletonList(inputStream).get(0) != null) {
                inputStream.close();
            }
        }
    }
}
~~~

####5、@Buidler 创建对象

#####5.1、通过注解@Buidler Lombok使用建造这模式创建和初始化对象

使用@Builder注释的方法可以帮助我们完成一下一些事情：

一个名为xxxBuilder的内部静态类，并具有和实体类形同的属性（称为构建器）。

1. 在构建器中：对于目标类中的所有的属性和未初始化的final字段，都会在构建器中创建对应属性；

2. 在构建器中：创建一个无参的default构造函数。

3. 在构建器中：对于实体类中的每个参数，都会对应创建类似于“setter”的方法，只不多方法名与该参数名相同。 并且返回值是构建器本身（便于链式调用），如上例所示。

4. 在构建器中：一个build()方法，调用此方法，就会根据设置的值进行创建实体对象。

5. 在构建器中：同时也会生成一个toString()方法。

6. 在实体类中：会创建一个builder()方法，它的目的是用来创建构建器。

   ~~~java
   Person.builder()
       .name("Adam Savage")
       .city("San Francisco")
       .job("Mythbusters")
       .job("Unchained Reaction")
       .build();
   ~~~

*注意：*

*如果使用@Builder的话切记所有私有全局变量都是需要显式赋值的，否则就是Null，不管你在原生T类中是否实例化，最终都是要被Builder的build()方法来重新实例化的，在添加了！Builder注解的类上要想自定义默认值，就要使用*@Builder.Default

##### 5.2、@Builder 中使用 @Singular 注释集合

使用`@Singular`注释注释一个集合字段 ，会为集合参数生成add 、addAll 、 clear方法，所以可以采用修改列表中一个元素而不是只能修改整个列表的方式，可以是增加一个元素，也可以是删除一个元素。 例如：

~~~java
Person.builder()
    .job("Mythbusters")
    .job("Unchained Reaction")
    .build();
~~~



