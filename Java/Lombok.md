# Lombok Annotation

### Lombok 이란? 

Lombok 프로젝트는 자바 라이브러리로 코드 에디터나 빌드 툴(IntelliJ, Eclipse, XCode 등)에 추가하여 코드를 효율적으로 작성할 수 있도록 도와준다. class명 위에 어노테이션을 명시해줌으로써 getter나 setter, equals와 같은 method를 따로 작성해야 하는 번거로움을 덜어준다.



### @RequiredArgsConstructor

@NonNull 이나 final이 붙은 필드에 대한 생성자를 생성해준다. 생성자 주입방식

```java
  /** @RequiredArgsConstructor 미사용 */
  @RestController
  public class AAAController {
      private AAAService aaaService;
      private BBBService bbbService;
      private CCCService cccService;

      public AAAController(AAAService aaaService, BBBService bbbService, CCCService cccService) {
          this.aaaService = aaaService;
          this.bbbService = bbbService;
          this.cccService = cccService;
      }
  }

  /** @RequiredArgsConstructor 사용 */
  @RestController
  @RequiredArgsConstructor
  public class AAAController {
      private final AAAService aaaService;
      private final BBBService bbbService;
      private final CCCService cccService;
  }
```



### @NoArgsConstructor

해당 어노테이션의 의미는 말 그대로 **"아무런 매개변수가 없는 생성자" 이다.**

```java
@Setter
@Getter
@NoArgsConstructor
public class testDto {
    private String id;
    private String userName;
    private String age;
    private String address;
}

// 같다.

@Setter
@Getter
public class testDto {
    private String id;
    private String userName;
    private String age;
    private String address;
  
    public testDto() {
    }
}
```

- 필드들이 FINAL 로 생성되어 있는 경우에는 필드를 초기화 할 수 없기 때문에 생성자를 만들 수 없고 에러가 발생하게 된다. 이때는 @NoArgsConstructor(force = true ) 옵션을 이용해서 FINAL 필드를 0, false, null 등으로 초기화를 강제로 시켜서 생성자를 만들 수 있다.

- @NonNull 같이 필드에 제약조건이 설정되어 있는 경우, 생성자내 null-check 로직이 생성되지 않는다. 후에 초기화를 진행하기 전까지 null-check 로직이 발생하지 않는 점을 염두하고 코드를 개발해야 한다.



Entity Class에는왜

- @NoArgsConstructor(access = AccessLevel.***PROTECTED***)을 사용할까?
- @NoArgsConstructor(access = AccessLevel.***PRIVATE***)는 안되는 건가?

private이 안되는 이유는 entity class에 사용되는 **proxy** 때문이다 (entity Proxy 조회).

또한 AccessLevel.PROTECTED를 사용하는 이유는, 기본 생성자의 접근 제어를 PROCTECTED 로 설정해놓게 되면 **무분별한 객체 생성에 대해 한번 더 체크할 수 있는 수단**이 되기 때문이다.

*참조하면 좋을 곳 [[link]](https://www.popit.kr/%EC%8B%A4%EB%AC%B4%EC%97%90%EC%84%9C-lombok-%EC%82%AC%EC%9A%A9%EB%B2%95/)*



### @AllargsConstructor

모든 매개변수 생성자. **해당 클래스 내의 모든 변수값을 가진 생성자를 자동으로 만들어 준다.**

```java
@AllArgsConstructor
public class testDto {
    private String id;
    private String userName;
    private String Age;
    private String address;
}

// 서로 같다.

public class testDto {
    private String id;
    private String userName;
    private String Age;
    private String address;

    public testDto(String id, String userName, String age, String address) {
        this.id = id;
        this.userName = userName;
        this.Age = age;
        this.address = address;
    }
}
```



### @Data

@Data 어노테이션은

- @Getter / @Setter
- @ToString
- @EqualsAndHashCode
- @RequiredArgsConstructor 

위 다섯 가지를 합쳐놓은 종합 선물 세트라고 할 수 있다. POJO(Plain Old Java Objects)와 bean과 관련된 모든 보일러플레이트(boilerplate =재사용 가능한 코드)를 생성한다. class의 모든 필드에 대한 getter, setter, toString, equals와 같은 함수들 말이다.



다음은 project lombok의 reference에 대한 내용이다. [[link]](https://projectlombok.org/features/Data)

> `@Data` is like having implicit `@Getter`, `@Setter`, `@ToString`, `@EqualsAndHashCode` and `@RequiredArgsConstructor` annotations on the class (**except that no constructor will be generated if any explicitly written constructors already exist**). However, the parameters of these annotations (such as `callSuper`, `includeFieldNames` and `exclude`) cannot be set with `@Data`. If you need to set non-default values for any of these parameters, just add those annotations explicitly; `@Data` is smart enough to defer to those annotations.
>
> - 만약 생성자가 이미 존재한다면, 생성자를 만들어 주지 않는다.
>
> All generated getters and setters will be `public`. To override the access level, annotate the field or class with an explicit `@Setter` and/or `@Getter` annotation. You can also use this annotation (by combining it with `AccessLevel.NONE`) to suppress generating a getter and/or setter altogether.
>
> - 모든 getter/setter는 Public이다. access level을 override 하기 위해서는, AccessLevel을 @setter/@getter 메소드에 명백하게 추가해야한다.
>
> If the class already contains a method with the same name and parameter count as any method that would normally be generated, that method is not generated, and no warning or error is emitted. For example, if you already have a method with signature `equals(AnyType param)`, no `equals` method will be generated, even though technically it might be an entirely different method due to having different parameter types. The same rule applies to the constructor (any explicit constructor will prevent `@Data` from generating one), as well as `toString`, `equals`, and all getters and setters. You can mark any constructor or method with `@lombok.experimental.Tolerate` to hide them from lombok.
>
> - 같은 이름의 메소드와 파라미터 개수가 존재한다면, @Data를 통해 생성되지 않는다.
>   - equals 메소드를 사용자가 정의한다면, @Data를 통해 equals 메소드가 생성되지 않는다.
>
> `@Data` can handle generics parameters for fields just fine. In order to reduce the boilerplate when constructing objects for classes with generics, you can use the `staticConstructor` parameter to generate a private constructor, as well as a static method that returns a new instance. This way, javac will infer the variable name. Thus, by declaring like so: `@Data(staticConstructor="of") class Foo<T> { private T x;}` you can create new instances of `Foo` by writing: `Foo.of(5);` instead of having to write: `new Foo<Integer>(5);`.
>
> - generic parameter를 사용할 수 있다. 
> - staticConstructor를 사용하면, private 생성자를 만들어주며, 객체 생성의 static method를 만들어준다.





##### @Data(staticConstructor = "of")

- 매개 변수의 값을 원하는 메소드 이름 (ex. "of" 는 default)으로 설정하면 **생성자를 private 으로 만들어 주고,** 주어진 이름의 **static factory method를 만들어준다.**

```java
@Data(staticConstructor="of")
public class User {
		private final String name; 
  
}


public class Addredss {

    static public void main(String []args){
        User.of("조재민");
    }
}
```



만약 인스턴스 변수 (name)이 final이 아니었다면? 

- staticConstructor에 의한 of() 메소드에서 인자로 사용될 수 없다. RequiredArgsConstructor 로 생성자 주입이 안되고, private 생성자만 만들어지기 때문이지 않을까.



