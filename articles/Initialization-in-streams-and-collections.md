Let's consider, we have to validate fields in the object according to some logic. I provide an example of this class.

```java
@Data
public class Example {
    private String a;
    private String b;
    private String c;
    private String d;
}
```
//непонятно, логика текстом написана, что поля завязаны друг на другу, а ты проверяешь чисто на null позже
All fields ~~can be nullable~~ are nullable?. If field "a" is present then field "b" has to be present too. If field "c" is blank then field "d" has to be blank too.
My first variant is
```java
public class Validator {

    public void validate(Example example) {
        List.of(example.getA(), example.getB(), example.getC(), example.getD())
                .forEach(this::validateSomeLogic);
    }
    
    private void validateSomeLogic(String example) {
        ...
    }
}
```

Assume, once we receive `null` for a field, in this case we get NPE when one of the fields is null.
The straightforward solution to prevent the error will be the null check definition before adding a value to the list.
Let's see what happens.
```java
public class Validator {

    public void validate(Example example) {
        List<String> list = new ArrayList<>();
        if (exapmle.getA() != null) list.add(example.getA());
        if (exapmle.getB() != null) list.add(example.getB());
        if (exapmle.getC() != null) list.add(example.getC());
        if (exapmle.getD() != null) list.add(example.getD());
        list.forEach(this::validateSomeLogic);
    }

    private void validateSomeLogic(String example) {
        ...
    }
}
```

It works but it looks a little bit clumsy. Do we have a way to do lazy initialization?
One of the differences between collections and streams is that streams support lazy initialization.
```java
public class Validator {

    public void validate(Example example) {
        Stream.of(example.getA(), example.getB(), example.getC(), example.getD())
                .filter(Objects::nonNull)
                .forEach(this::validateSomeLogic);
    }

    private void validateSomeLogic(String example) {
        ...
    }
}
```
It looks much better! I think this example is good to demonstrate the differences between types of initializations in collections and streams.
