
# 1주차

### **1. MVC 패턴이란**

MVC : model-view-controller. 하나의 애플리케이션, 프로젝트를 구성할 때 그 구성요소를 세가지의 역할로 구분함.

사용자 인터페이스, 데이터 및 논리 제어를 구현하는데 널리 사용되는 소프트웨어 디자인 패턴

- 모델: 데이터와 비즈니스 로직을 관리
- 뷰: 레이아웃과 화면을 처리
- 컨트롤러: 명령을 모델과 뷰 부분으로 라우팅

예시)

Controller

```java
@Controller
public class HelloController {
@GetMapping("hello-mvc")
public String helloMvc(@RequestParam("name") String name, Model model) {
model.addAttribute("name", name);
return "hello-template";
}
}
```

View

resources/templates/hello-template.html

```xml
<html xmlns:th="http://www.thymeleaf.org">
<body>
<p th:text="'hello ' + ${name}">hello! empty</p>
</body>
</html>
```

실행 --> http://localhost:8080/hello-mvc?name=spring

<br>

### **2. API와 서버?**

- API : Application Programming Interface. 애플리케이션에서 제공하는 인터페이스.
    - api를 통해 서버 또는 프로그램 사이를 연결할 수 있다.
- API 예시

```java
@Controller
public class HelloController {

    @GetMapping("hello")
    public String hello(Model model){
        model.addAttribute( "data", "hello!!");
        return "hello"; // hello.html
    }

    @GetMapping("hello-mvc") // http://localhost:8080/hello-string?name=spring
    public String helloMvc(@RequestParam(value="name") String name, Model model) {
        model.addAttribute("name", name);
        return "hello-template";
    }

    @GetMapping("hello-string")
    @ResponseBody
    public String helloString(@RequestParam("name") String name) {
        return "hello " + name; // HTML 태그 없이 문자 그대로 넘겨짐
    }

    // API 방식
    @GetMapping("hello-api")
    @ResponseBody
    public Hello helloApi(@RequestParam("name") String name) {
        Hello hello = new Hello();
        hello.setName(name);
        return hello; // json 방식. {"name":"입력값"}
    }

    static class Hello {
        private String name;

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }
    }
}
```

- Response Body : 무거운 XML 방식보다 JSON 방식 선호
- Bean : Spring에 의하여 생성되고 관리되는 자바 객체.

기존의 Java Programming 에서는 Class를 생성하고 new를 입력하여 원하는 객체를 직접 생성한 후에 사용했었다.

하지만 Spring에서는 직접 new를 이용하여 생성한 객체가 아니라, Spring에 의하여 관리당하는 자바 객체를 사용한다.

Spring Framework 에서는 Spring Bean 을 얻기 위하여 **getter, setter**  (예: ApplicationContext.getBean()) 와 같은 메소드를 사용하여 Spring 에서 직접 자바 객체를 얻어서 사용한다.

<br>

### **@ResponseBody 사용원리**

![https://blog.kakaocdn.net/dn/LbJlR/btr53EDGUzZ/ohQo89kNM0ezx7SF2OdXL1/img.png](https://blog.kakaocdn.net/dn/LbJlR/btr53EDGUzZ/ohQo89kNM0ezx7SF2OdXL1/img.png)

@ResponseBody 어노테이션 없으면 html 파일 찾아서 템플릿 엔진에서 처리한다. (리턴값: 문자)

어노테이션 있으면 : 리턴값이 객체. 객체가 오면 디폴트로 JSON 방식으로 데이터를 만들어서 HTTP 응답에 반환함.

- HTTP의 BODY에 문자 내용을 직접 반환
- viewResolver 대신에 HttpMessageConverter 가 동작
- 기본 문자처리: StringHttpMessageConverter
- 기본 객체처리: MappingJackson2HttpMessageConverter
- byte 처리 등등 기타 여러 HttpMessageConverter가 기본으로 등록되어 있음
- 클라이언트의 HTTP Accept 해더와 서버의 컨트롤러 반환 타입 정보 둘을 조합해서HttpMessageConverter 가 선택된다.
- 

<br>

### **3. RESTful 이란?**

**REST 아키텍처**를 구현하는 웹 서비스는 ‘RESTful하다’ 고 표현함.

- **REST API** : REST 아키텍쳐를 따르는 시스템/애플리케이션 인터페이스
- **REST 아키텍처** : Representational State Transfer. WWW와 같은 분산 하이퍼미디어시스템 아키텍처의 한 형식.주고받는 자원에 이름을 규정하고, URI에 명시해서, HTTP 메서드(GET, POST, PUT, DELETE)를 통해 해당 자원의 상태를 주고받는 것.
