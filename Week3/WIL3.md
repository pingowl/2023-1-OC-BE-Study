# 3주차

****목표****

**스프링 입문 - 코드로 배우는 스프링 부트, 웹 MVC, DB 접근 기술 강의 Section 4 ~ 7 을 듣고,**

**Autowired 의존성 주입,  DIP, 스프링 빈 + 스프링 컨테이너, JDBC와 JPA에 대해 학습해봅시다.**

---

### 1.의존과 의존성

- 의존 : 사용하는 것
- 의존성 : 다른 객체나 함수를 사용하는 상태

MemberController가 MemberService를 의존하는 경우.

MemberController가 MemberService를 사용해 멤버의 정보도 확인하고, 맴버를 관리할 수 있다

```java
@Controller
public class MemberController {

	private final MemberService memberService = new MemberService();
	...
}
```

객체지향 프로그래밍은 의존성이나 결합도를 적절히 낮추는 방식을 지향한다.
의존성을 주입 받는 것은 의존성을 낮추는 좋은 방법 중 하나이다.

**의존성 주입(DI)**은 스프링 프레임워크의 핵심 프로그래밍 모델 중 하나.

**DI**란 외부에서 두 객체 간의 관계를 결정해주는 디자인 패턴으로, 인터페이스를 사이에 둬서 클래스 레벨에서는 의존관계가 고정되지 않도록 하고 런타임 시에 관계를 동적으로 주입하여 유연성을 확보하고 결합도를 낮출 수 있게 해준다.

<br>

### 2.@Autowired 의존성 주입

의존성 주입 방법 3가지

- **Field Injection**
    
    의존성을 주입하고자 하는 필드에 @Autowired 애노테이션을 붙인다.
    
    ```java
    @RestController
    public class MemberController {
    
        @Autowired
        private MemberService memberService;
    
    }
    ```
    
    1. 주입받으려는 빈의 생성자를 호출하여 빈을 찾거나 빈 팩토리에 등록
    2. 생성자 인자에 사용하는 빈을 찾거나 만듬
    3. 필드에 주입
    
    해당 방식의 문제는 주입할 빈이 없어도 빈 생성이 가능하다는 것이다. 즉, 필드에 의존을 주입하지 않아도 스프링에서 에러가 터지지 않는다.
    
    그래서 앱이 구동된 이후 실제로 memberService에 접근할 때 비로소 NPE가 발생
    
<br>

- **Setter based Injection**
    
    Setter 메서드에 @Autowired 어노테이션을 붙여 의존성을 주입하는 방식
    
    ```java
    @RestController
    public class MemberController {
    
        private MemberService MemberService;
    
        @Autowired
        public void setMemberService(MemberService MemberService){
            this.MemberService = MemberService;
        }
    
    }
    ```
    
    필드에 바로 주입하는 것이 아니라 주입하려는 빈 객체의 수정자(Setter)를 호출하여 주입을 한다.
    
    주입할 빈이 없어도 해당 빈이 생성되고 에러 처리가 되지 않는다. 그래서 memberService에 접근할 때 (예제 코드 기준) 비로소 NPE가 발생한다.
    
<br>

- **Constructor based Injection**
    
    생성자를 사용해서 의존성을 주입하는 방식
    
    ```java
    @RestController
    public class MemberController {
    
        private final MemberService MemberService;
    
        public MemberController(MemberService MemberService){
            this.MemberService = MemberService;
        }
    
    }
    ```
    
    객체가 생성되는 시점에 빈을 주입하기 때문에 주입할 빈이 없다면 에러가 발생한다. 즉, 앱 구동 시점에 즉시 에러를 확인할 수 있다.
    

**[추가자료](https://jgrammer.tistory.com/entry/springboot-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%A3%BC%EC%9E%85-%EB%B0%A9%EC%8B%9D-%EA%B2%B0%EC%A0%95)

<br>

### 3.DIP

: Dependency Inversion principle. 의존성 역전 원칙

**`SOLID`** 원칙 중 하나.

하위 레벨 모듈의 변경이 상위 레벨 모듈까지 전파되는 구조적 문제에서 발생하는 위계관계를 끊기 위한 원칙이다.

아래 2가지 원칙을 따른다.

- 상위 모듈은 하위 모듈에 종속되면 안되고 둘 다 **`추상화`**에 의존해야 한다.
- 추상화는 세부사항에 의존하면 안 되고, 세부사항은 추상화에 의존해야 한다.

- 장점: 변화하지 않는 추상화에 의존함으로써, 확장에 유연하고 변경에 폐쇄적인 설계가 가능하다.즉, `OCP`를 지킬 수 있다.

<br>

### 4.스프링 빈과 스프링 컨테이너란?

- **스프링 빈**은 스프링 컨테이너에 등록되어 관리되는 객체를 말한다. 즉, 스프링 빈은 스프링이 직접 생성하고 관리하는 객체로서, 개발자가 직접 객체를 생성하고 관리하지 않아도 된다.

- **스프링 컨테이너**는 스프링 빈을 관리하며, 스프링 빈을 생성하고 의존성을 주입해주는 등의 다양한 기능을 제공한다. 스프링 컨테이너는 스프링 빈을 생성하고 관리하는 역할을 담당하며, 개발자가 직접 객체를 생성하고 관리하는 것보다 유연하게 객체를 제공할 수 있다.

- 개발자가 객체들을 생성, 소멸시키는 것과 같은 관리를 하지 않고 컨테이너라는 관리자가 알아서 잘 관리해주는 자동 사냥 시스템을 갖춘 프레임워크이다.

<br>

### 5.JDBC와 JPA?

- **공통점**
JDBC와 JPA 모두 데이터베이스와 연동하여 데이터를 조회, 삽입, 수정, 삭제 등의 작업을 수행할 수 있는 API이다.
둘 다 자바에서 데이터베이스를 다루는 기술.

- **차이점**
    - JDBC는 SQL 쿼리를 직접 작성하여 데이터베이스를 다루는 반면, **JPA**는 객체와 관계형 데이터베이스 간의 **매핑**을 처리하는 ORM 기술.
    - JDBC는 각 기능을 직접 구현해야 하기 때문에 반복적인 코드 작성이 필요. 반면, JPA는 객체와 테이블 간의 매핑을 처리하기 때문에 이를 처리하는 코드를 작성할 필요가 없어 편리.
    - JDBC는 테이블과 컬럼의 변경이 발생하면 직접 쿼리를 수정해야 하기 때문에 유지 보수가 어렵다. 반면, JPA는 객체와 테이블 간의 매핑을 처리하기 때문에 데이터베이스의 변경 사항이 있더라도 애플리케이션 코드를 수정할 필요가 없어서 유지보수가 용이하다.
    - JPA는 객체 지향적인 코드 작성이 가능하여 개발자가 코드를 더욱 직관적으로 작성할 수 있고, 객체 간의 관계를 더욱 쉽게 처리할 수 있다는 장점이 있다.