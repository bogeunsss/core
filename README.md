# 스프링 핵심 원리 - 기본편
## 1. 예제 만들기

### 프로젝트 생성

- 추가적인 dependency를 적용하지 않고, spring core만으로 프로젝트를 생성했다.



### 비즈니스 요구사항과 설계

- 회원
  - 회원 가입 및 조회할 수 있다.
  - 회원은 일반과 VIP 두 가지 등급이 있다.
  - 회원 데이터는 자체 DB를 구축할 수 있고, 외부 시스템과 연동할 수 있다.(미확정)
  
- 주문과 할인 정책
  - 회원은 상품을 주문할 수 있다.
  - 회원 등급에 따라 할인 정책을 적용할 수 있다.
  - 할인 정책은 모든 VIP는 1,000원을 할인해주는 고정 금액 할인을 적용해달라.(나중에 변경될 수 있다.)
  - 할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루고 싶다. 최악의 경우 할인을 적용하지 않을 수도 있다.(미확정)



### 회원 도메인 설계

![회원 도메인 협력 관계](./assets/회원_도메인_협력_관계.jpg)

![회원 클래스 다이어그램](./assets/회원_클래스_다이어그램.jpg)

![회원 객체 다이어그램](./assets/회원_객체_다이어그램.jpg)



### 주문과 할인 도메인 설계

- 주문과 할인 정책
  - 회원은 상품을 주문할 수 있다.
  - 회원 등급에 따라 할인 정책을 적용할 수 있다.
  - 할인 정책은 모든 VIP는 1,000원을 할인해주는 고정 금액 할인을 적용해달라.(나중에 변경될 수 있다.)
  - 할인 정책은 변경 가능성이 높다. 회사의 기본 할인 정책을 아직 정하지 못했고, 오픈 직전까지 고민을 미루고 싶다. 최악의 경우 할인을 적용하지 않을 수도 있다.(미확정)



![주문 도메인 회원 역할 책임](./assets/주문_도메인_회원_역할_책임.jpg)

1. **주문 생성** : 클라이언트는 주문 서비스에 주문 생성을 요청한다.
2. **회원 조회** : 할인을 위해서는 회원 등급이 필요하다. 그래서 주문 서비스는 회원 저장소에서 회원을 조회한다.
3. **할인 적용** : 주문 서비스는 회원 등급에 따른 할인 여부를 할인 정책에 위임한다.
4. **주문 결과 반환** : 주문 서비스는 할인 결과를 포함한 주문 결과를 반환한다.



![주문 도메인 전체](./assets/주문_도메인_전체.JPG)

**역할과 구현을 분리**해서 자유롭게 구현 객체를 조립할 수 있게 설계했다. 덕분에 회원 저장소는 물론이고, 할인 정책도 유연하게 변경할 수 있다.



![주문 도메인 클래스 다이어그램](./assets/주문_도메인_클래스_다이어그램.jpg)



![주문 도메인 객체 다이어그램1](./assets/주문_도메인_객체_다이어그램1.jpg)

회원을 메모리에서 조회하고, 정액 할인 정책(고정 금액)을 지원해도 주문 서비스를 변경하지 않아도 된다. 역할들의 협력 관계를 그대로 재사용 할 수 있다.



![주문 도메인 객체 다이어그램2](./assets/주문_도메인_객체_다이어그램2.jpg)

회원을 메모리가 아닌 실제 DB에서 조회하고, 정률 할인 정책(주문 금액에 따라 % 할인)을 지원해도 주문 서비스를 변경하지 않아도 된다. 협력 관계를 그대로 재사용 할 수 있다.



## 2. 객체 지향 원리 적용

### 새로운 할인 정책 개발

##### 새로운 할인 정책을 확장해보자.

- **악덕 기획자** : 서비스 오픈 직전에 할인 정책을 지금처럼 고정 금액 할인이 아니라 좀 더 합리적인 주문 금액당 할인하는 정률(%) 할인으로 변경하고 싶어요. 예를 들어 기존 정책은 VIP가 10,000원을 주문하든 20,000원을 주문하든 항상 1,000원을 할인했는데, 이번에 새로 나온 정책은 10%로 지정해두면 고객이 10,000원 주문시 1,000원을 할인해주고, 20,000원을 주문시에 2,000원을 할인해주는 거에요!
- **순진 개발자** : 제가 처음부터 고정 금액 할인은 아니라고 했잖아요.
- **악덕 기획자** : 애자일 소프트웨어 개발 선언 몰라요? 계획을 따르기보다 변화에 대응하기를
- **순진 개발자** : ...(하지만 난 유연한 설계가 가능하도록 객체지향 설계 원칙을 준수했지)

> 참고 : 애자일 소프트웨어 개발 선언(https://agilemanifesto.org/iso/ko/manifesto.html)

순진 개발자가 정말 객체지향 설계 원칙을 잘 준수했는지 확인해보자. 이번에는 주문한 금액의 %를 할인해주는 새로운 정률 할인 정책을 추가하자.



![RateDiscountPolicy추가](./assets/RateDiscountPolicy_추가.jpg)



### 새로운 할인 정책 적용과 문제점

##### 방금 추가한 할인 정책을 애플리케이션에 적용해보자.

할인 정책을 변경하려면 클라이언트인 `OrderServiceImpl` 코드를 고쳐야 한다.

```java
public class OrderServiceImpl implements OrderService {
    // private final DiscountPolicy discountPolicy = new FixDiscountPolicy();
    private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
}
```

**문제점 발견**

- 우리는 역할과 구현을 충실하게 분리했다. → OK
- 다형성도 활용하고, 인터페이스와 구현 객체를 분리했다. → OK
- OCP, DIP 같은 객체지향 설계 원칙을 충실히 준수했다.
  - → 그렇게 보이지만 사실은 아니다.
- DIP : 주문서비스 클라이언트(`OrderServiceImpl`)은 `DiscountPolicy` 인터페이스에 의존하면서 DIP를 지킨것 같은데?
  - → 클래스 의존관계를 분석해보자. 추상(인터페이스) 뿐만 아니라 **구체(구현) 클래스**에도 의존하고 있다.
    - 추상(인터페이스) 의존 : `DiscountPolicy`
    - 구체(구현) 클래스 의존 : `FixDiscountPolicy`, `RateDiscountPolicy`
- OCP : 변경하지 않고 확장할 수 있다고 했는데!
  - → **지금 코드는 기능을 확장해서 변경하면, 클라이언트 코드에 영향을 준다!** 따라서 **OCP를 위반**한다.



**왜 클라이언트 코드를 변경해야 할까?**
클래스 다이어그램으로 의존관계를 분석해보자.
![기대했던 의존관계](./assets/기대했던_의존관계.jpg)
지금까지 단순히 `DiscountPolicy` 인터페이스마 의존한다고 생각했다.

![실제 의존관계](./assets/실제_의존관계.jpg)
잘보면 클라이언트인 `OrderServiceImpl`이 `DiscountPolicy` 인터페이스 뿐만 아니라 `FixDiscountPolicy`인 구체 클래스도 함께 의존하고 있다. 실제 코드를 보면 의존하고 있다! **DIP 위반**

![정책 변경](./assets/정책_변경.jpg)

**중요!** 그래서 `FixDiscountPolicy`를 `RateDiscountPolicy`로 변경하는 순간 `OrderServiceImpl`의 소스코드도 함께 변경해야 한다! **OCP 위반**



**어떻게 문제를 해결할 수 있을까?**

- 클라이언트 코드인 `OrderServiceImpl`은 `DiscountPolicy`의 인터페이스 뿐만 아니라 구체 클래스도 함께 의존한다.
- 그래서 구체 클래스를 변경할 때 클라이언트 코드도 함께 변경해야한다.
- **DIP 위반** → 추상에만 의존하도록 변경(인터페이스에만 의존)
- DIP를 위반하지 않도록 인터페이스에만 의존하도록 의존관계를 변경하면 된다.

**인터페이스에만 의존하도록 설계를 변경하자**
![인터페이스에만 의존하도록 설계를 변경하자](./assets/인터페이스에만_의존하도록_설계를_변경하자.jpg)



**인터페이스에만 의존하도록 코드 변경**

```java
public class OrderServiceImpl implements OrderService {
    // private final DiscountPolicy discountPolicy = new RateDiscountPolicy();
    private DiscountPolicy discountPolicy;
}
```

- 인터페이스에만 의존하도록 설계와 코드를 변경했다.
- **그런데 구현체가 없는데 어떻게 코드를 실행할 수 있을까?**
- 실제 실행을 해보면 NPE(null pointer exception)가 발생한다.



**해결방안**

- 이 문제를 해결하려면 누군가가 클라이언트인 `OrderServiceImpl`에 `DiscountPolicy`의 구현 객체를 대신 생성하고 주입해주어야 한다.



### 관심사의 분리

- 애플리케이션을 하나의 공연이라 생각해보자. 각각의 인터페이스를 배역(배우 역할)이라 생각하자. 그런데! 실제 배역에 맞는 배우를 선택하는 것은 누가 하는가?
- 로미오와 줄리엣 공연을 하던 로미오 역할을 누가 할지는 배우들이 정하는 게 아니다. 이전 코드는 마치 로미오 역할(인터페이스)을 하는 레오나르도 디카프리오(구현체, 배우)가 줄리엣 역할(인터페이스)을 하는 여자 주인공(구현체, 배우)를 직접 초빙하는 것과 같다. 디카프리오는 공연도 해야하고 동시에 여자 주인공도 공연에 직접 초빙해야 하는 **다양한 책임**을 가지고 있다.



**관심사를 분리하자**

- 배우는 본인의 역할인 배역을 수행하는 것에만 집중해야 한다.
- 디카프리오는 어떤 여자 주인공이 선택되더라도 똑같이 공연을 할 수 있어야 한다.
- 공연을 구성하고, 담당 배우를 섭외하고, 역할에 맞는 배우를 지정하는 책임을 담당하는 별도의 **공연 기획자**가 나올 시점이다.
- 공연 기획자를 만들고, 배우와 공연 기획자의 책임을 확실히 분리하자.



#### AppConfig의 등장

- 애플리케이션의 전체 동작 방식을 구성(config)하기 위해, **구현 객체를 생성**하고, **연결**하는 책임을 가지는 별도의 설정 클래스를 만들자.

  **AppConfig**

  ```java
  public class AppConfig {
      
      public MemberService memberService() {
          return new MemberServiceImpl(new MemoryMemberRepository());
      }
      
      public OrderService orderService() {
          return new OrderServiceImpl(
          	new MemoryMemberRepository(),
              new FixDiscountPolicy()
          );
      }
  }
  ```

- AppConfig는 애플리케이션의 실제 동작에 필요한 **구현 객체를 생성**한다.
  - `MemberServiceImpl`
  - `MemoryMemberRepository`
  - `OrderServiceImple`
  - `FixDiscountPolicy`
- AppConfig는 생성한 객체 인스턴스의 참조(레퍼런스)를 **생성자를 통해 주입(연결)**해준다.
  - `MemberServiceImpl` → `MemoryMemberRepository`
  - `OrderServiceImpl` → `MemoryMemberRepository`, `FixDiscountPolicy`



> 참고

지금은 각 클래스에 생성자가 없어서 컴파일 오류가 발생한다. 바로 다음에 코드에서 생성자를 만든다.



**MemberServiceImpl - 생성자 주입**

```java
public class MemberServiceImpl implements MemberService{

    private final MemberRepository memberRepository;

    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

    @Override
    public void join(Member member) {
        memberRepository.save(member);
    }

    @Override
    public Member findMember(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

- 설계 변경으로 `MemberServiceImpl`은 `MemoryMemberRepository`를 의존하지 않는다.
- 단지 `MemberRepository` 인터페이스에만 의존한다.
- `MemberServiceImpl` 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.
- `MemberServiceImpl`의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(`AppConfig`)에서 결정된다.
- `MemberServiceImpl`은 이제부터 **의존관계에 대한 고민은 외부**에 맡기고 **실행에만 집중**하면 된다.



![AppConfig 클래스 다이어그램](./assets/AppConfig_클래스_다이어그램.jpg)

- 객체의 생성과 연결은 `AppConfig`가 담당한다.
- **DIP 완성** : `MemberServiceImpl`은 `MemberRepository`인 추상에만 의존하면 된다. 이제 구체 클래스를 몰라도 된다.
- **관심사의 분리** : 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리되었다.



![AppConfig 객체 인스턴스 다이어그램](./assets/AppConfig_객체_인스턴스_다이어그램.jpg)

- `appConfig` 객체는 `memoryMemberRepository` 객체를 생성하고 그 참조값을 `memberServiceImpl`을 생성하면서 생성자로 전달한다.
- 클라이언트인 `memberServiceImpl` 입장에서 보면 의존관계를 마치 외부에서 주입해주는 것과 같다고 해서 DI(Dependency Injection) 우리말로 의존관계 주입 또는 의존성 주입이라고 한다.



**OrderServiceImpl - 생성자 주입**

```java
public class OrderServiceImpl implements OrderService{

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

    @Override
    public Order createOrder(Long memberId, String itemName, int itemPrice) {
        Member member = memberRepository.findById(memberId);
        int discountPrice = discountPolicy.discount(member, itemPrice);

        return new Order(memberId, itemName, itemPrice, discountPrice);
    }
}
```

- 설계 변경으로 `OrderServiceImpl`은 `FixDiscountPolicy`를 의존하지 않는다.
- 단지 `DiscountPolicy` 인터페이스에만 의존한다.
- `OrderServiceImpl` 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없다.
- `OrderServiceImpl`의 생성자를 통해서 어떤 구현 객체를 주입할지는 오직 외부(`AppConfig`)에서 결정한다.
- `OrderServiceImpl`은 이제부터 실행에만 집중하면 된다.
- `OrderServiceImpl`에는 `MemoryMemberRepository`, `FixDiscountPolicy` 객체의 의존관계가 주입된다.



#### AppConfig 실행

> 테스트 코드에서 `@BeforeEach`는 각 테스트를 실행하기 전에 호출된다.



**정리**

- AppConfig를 통해서 관심사를 확실하게 분리했다.
- 배역, 배우를 생각해보자.
- AppConfig는 공연 기획자다.
- AppConfig는 구체 클래스를 선택한다. 배역에 맞는 담당 배우를 선택한다. 애플리케이션이 어떻게 동작해야 할지 전체 구성을 책임진다.
- 이제 각 배우들은 담당 기능을 실행하는 책임만 지면 된다.
- `OrderServiceImpl`은 기능을 실행하는 책임만 지면 된다.



### 새로운 구조와 할인 정책 적용

- 처음으로 돌아가서 정액 할인 정책을 정률% 할인 정책으로 변경해보자.
- FixDiscountPolicy → RateDiscountPolicy
- 어떤 부분만 변경하면 되겠는가?

**AppConfig의 등장으로 애플리케이션이 크게 사용 영역과 객체를 생성하고 구성(Configuration)하는 영역으로 분리되었다.**

**그림 - 사용, 구성의 분리**
![사용, 구성의 분리](./assets/사용_및_구성의_분리.jpg)



**그림 - 할인 정책의 변경**
![할인 정책의 변경](./assets/할인_정책의_변경.jpg)

- `FixDiscountPolicy` → `RateDiscountPolicy`로 변경해도 구성 영역만 영향을 받고, 사용 영역은 전혀 영향을 받지 않는다.
- `AppConfig`에서 할인 정책 역할을 담당하는 구현을 `FixDiscountPolicy` → `RateDiscountPolicy` 객체로 변경했다.
- 이제 할인 정책을 변경해도, 애플리케이션의 구성 역할을 담당하는 AppConfig만 변경하면 된다. 클라이언트 코드인 `OrderServiceImpl`을 포함해서 **사용 영역**의 어떤 코드도 변경할 필요가 없다.
- **구성 영역**은 당연히 변경된다. 구성 역할을 담당하는 AppConfig를 애플리케이션이라는 공연 기획자로 생각하자. 공연 기획자는 공연 참여자인 구현 객체들을 모두 알아야 한다.


### 전체 흐름 정리

- 새로운 할인 정책 개발
- 새로운 할인 정책 적용과 문제점
- 관심사의 분리
- AppConfig 리팩터링
- 새로운 구조와 할인 정책 적용
  

**새로운 할인 정책 개발**

​	다형성 덕분에 새로운 정률 할인 정책 코드를 추가로 개발하는 것 자체는 아무 문제가 없음


**새로운 할인 정책 적용과 문제점**

​	새로 개발한 정률 할인 정책을 적용하려고 하니 **클라이언트 코드**인 주문 서비스 구현체도 함께 변경해야함
​	주문 서비스 클라이언트가 인터페이스인 `DiscountPolicy` 뿐만 아니라, 구체 클래스인 `FixDiscountPolicy`	도 함께 의존 → **DIP 위반**

**관심사의 분리**

- 애플리케이션을 하나의 공연으로 생각
- 기존에는 클라이언트가 의존하는 서버 구현 객체를 직접 생성하고 실행함
- 비유를 하면 기존에는 남자 주인공 배우가 공연도 하고, 동시에 여자 주인공도 직접 초빙하는 다양한 책임을 가지고 있음
- 공연을 구성하고, 담당 배우를 섭외·지정하는 책임을 별도로 담당하는 **공연 기획자**가 나올 시점
- 공연 기획자인 AppConfig가 등장
- AppConfig는 애플리케이션의 전체 동작 방식을 구성(Configuration)하기 위해 **구현 객체를 생성**하고, **연결**하는 책임
- 이제부터 클라이언트 객체는 자신의 역할을 실행하는 것만 집중, 권한이 줄어듬(책임이 명확해짐)
  

**AppConfig 리팩터링**

- 구성 정보에서 역할과 구현을 명확하게 분리
- 역할이 잘 드러남
- 중복 제거
  

**새로운 구조와 할인 정책 적용**

- 정액 할인 정책 → 정률% 할인 정책으로 변경
- AppConfig의 등장으로 애플리케이션이 크게 **사용 영역**과 객체를 생성하고 **구성(Configuration)하는 영역**으로 분리
- 할인 정책을 변경해도 AppConfig가 있는 구성 영역만 변경하면 됨. 사용 영역은 변경할 필요가 없음. 물론 클라이언트 코드인 주문 서비스 코드도 변경하지 않음



### 좋은 객체 지향 설계의 5가지 원칙 적용

> 여기서 3가지 SRP, DIP, OCP 적용



#### SRP 단일 책임의 원칙

**한 클래스는 하나의 책임만 가져야 한다.**

- 클라이언트 객체는 직접 구현 객체를 생성하고, 연결하고, 실행하는 다양한 책임을 가지고 있음
- SRP 단일 책임 원칙을 따르면서 관심사를 분리함
- 구현 객체를 생성하고 연결하는 책임은 AppConfig가 담당
- 클라이언트 객체는 실행하는 책임만 담당



#### DIP 의존관계 역전의 원칙

**프로그래머는 "추상화에 의존해야지, 구체화에 의존하면 안된다." 의존성 주입은 이 원칙을 따르는 방법 중 하나다.**

- 새로운 할인 정책을 개발하고 적용하려고 하니, 클라이언트 코드도 함께 변경해야 했다. 왜냐하면 기존 클라이언트 코드(`OrderServiceImpl`)는 DIP를 지키며 `DiscountPolicy` 추상화 인터페이스에 의존하는 것 같았지만, `FixDiscountPolicy` 구체화 구현 클래스에도 함께 의존했다.
- 클라이언트 코드가 `DiscountPolicy` 추상화 인터페이스에만 의존하도록 코드를 변경했다.
- 하지만 클라이언트 코드는 인터페이스만으로는 아무것도 실행할 수 없다.
- AppConfig가 `FixDiscountPolicy` 객체 인스턴스를 클라이언트 코드 대신 생성해서 클라이언트 코드에 의존관계를 주입했다. 이렇게 해서 DIP 원칙을 따르고 문제도 해결했다.



#### OCP 개방 폐쇄의 원칙

**소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.**

- 다형성을 사용하고 클라이언트가 DIP를 지킴
- 애플리케이션을 사용 영역과 구성 영역을 나눔
- AppConfig가 의존관계를 `FixDiscountPolicy` → `RateDiscountPolicy`로 변경해서 클라이언트 코드에 주입하므로 클라이언트 코드는 변경하지 않아도 됨
- **소프트웨어 요소를 새롭게 확장해도 사용 영역의 변경은 닫혀 있다!**



### IoC, DI, 컨테이너

#### 제어의 역전 IoC(Inversion of Control)

- 기존 프로그램은 클라이언트 구현 객체가 스스로 필요한 서버 구현 객체를 생성-연결-실행 했다. 한마디로 구현 객체가 프로그램의 제어 흐름을 스스로 조종했다. 개발자 입장에서는 자연스러운 흐름이다.
- 반면에 AppConfig가 등장한 이후에 구현 객체는 자신의 로직을 실행하는 역할만 담당한다. 프로그램의 제어 흐름은 이제 AppConfig가 가져간다. 예를 들어서 `OrderServiceImpl`은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모른다.
- 프로그램의 제어 흐름에 관한 권한은 모두 AppConfig가 가지고 있다. 심지어 `OrderServiceImpl`도 AppConfig가 생성한다. 그리고 AppConfig는 `OrderServiceImpl`이 아닌 OrderService 인터페이스의 다른 구현 객체를 생성하고 실행할 수도 있다. 그런 사실도 모른체 `OrderServiceImpl`은 묵묵히 자신의 로직을 실행할 뿐이다.
- 이렇듯 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)이라 한다.



**프레임워크 vs 라이브러리**

- 프레임워크가 내가 작성한 코드를 제어하고 대신 실행한다면 그것은 프레임워크가 맞다. → ex) JUnit
- 반면에 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 프레임워크가 아니라 라이브러리이다.



#### 의존관계 주입 DI(Dependency Injection)

- `OrderServiceImpl`은 `DiscountPolicy` 인터페이스에 의존한다. 실제 어떤 구현 객체가 사용될지는 모른다.

- 의존관계는 **정적인 클래스 의존 관계와 실행 시점에 결정되는 동적인 객체(인스턴스) 의존 관계** 둘을 분리해서 생각해야 한다.

  **정적인 클래스 의존 관계**

  클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단할 수 있다. 정적인 의존 관계는 애플리케이션을 실행하지 않아도 분석할 수 있다. <u>클래스 다이어그램을 보자!</u>

  - `OrderServiceImpl`은 `MemberRepository`, `DiscountPolicy`에 의존한다는 것을 알 수 있다.
  - 그런데 이러한 클래스 의존 관계만으로는 실제 어떤 객체가 `OrderServiceImpl`에 주입될지 알 수 없다.

  **DI, 클래스 다이어그램**
  ![DI, 클래스 다이어그램](./assets/DI_클래스_다이어그램.jpg)**동적인 객체 인스턴스 의존 관계**

  애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존 관계

  **DI, 객체 다이어그램**

  ![DI, 객체 다이어그램](./assets/DI_객체_다이어그램.jpg)

- 애플리케이션 **실행 시점(런타임)**에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존 관계가 연결 되는 것을 **의존 관계 주입**이라고 한다.
- 객체 인스턴스를 생성하고, 그 참조값을 전달해서 연결한다.
- 의존 관계 주입을 사용하면 클라이언트 코드를 변경하지 않고, 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있다.
- 의존 관계 주입을 사용하면 정적인 클래스 의존 관계를 변경하지 않고, 동적인 객체 인스턴스 의존 관계를 쉽게 변경할 수 있다.



**IoC 컨테이너, DI 컨테이너**

- AppConfig처럼 객체를 생성하고 관리하면서 의존 관계를 연결해주는 것을 **IoC 컨테이너** 또는 **DI 컨테이너**라고 한다.
- 의존 관계 주입에 초점을 맞추어 최근에는 주로 **DI 컨테이너**라고 한다.
- 어샘블러, 오브젝트 팩토리 등으로 불리기도 한다.



#### Spring 기반으로 변경

### Spring 기반으로 변경

**AppConfig.java**

- AppConfig에 설정을 구성한다는 뜻의 `@Configuration`을 붙여준다.
- 각 메서드에 `@Bean`을 붙여준다. 이렇게 하면 스프링 컨테이너에 스프링 빈으로 등록한다.



**스프링 컨테이너**

- `ApplicationContext`를 스프링 컨테이너라 한다.
- 기존에는 개발자가 `AppConfig`를 사용해서 직접 객체를 생성하고 DI를 했지만, 이제부터는 스프링 컨테이너를 통해서 사용한다.
- 스프링 컨테이너는 `@Configuration`이 붙은 `AppConfig`를 설정(구성) 정보로 사용한다. 여기서 `@Bean`이라 적힌 메서드를 모두 호출해서 반한된 객체를 스프링 컨테이너에 등록한다. 이렇게 스프링 컨테이너에 등록된 객체를 **스프링 빈**이라 한다.
- 스프링 빈은 `@Bean`이 붙은 메서드의 이름을 스프링 빈의 이름으로 사용한다. → `memberService`, `orderService`
- 이전에는 개발자가 필요한 객체를 `AppConfig`를 사용해서 직접 조회했지만, 이제부터는 스프링 컨테이너를 통해서 필요한 스프링 빈(객체)를 찾아야 한다. 스프링 빈은 `ApplicationContext.getBean()` 메서드를 사용해서 찾을 수 있다.
- 기존에는 개발자가 직접 자바 코드로 모든 것을 했다면, 이제부터는 스프링 컨테이너에 객체를 스프링 빈으로 등록하고, 스프링 컨테이너에서 스프링 빈을 찾아서 사용하도록 변경되었다.
- 코드가 약간 더 복잡해진 것 같은데, 스프링 컨테이너를 사용하면 어떤 장점이 있을까?





## 3. 스프링 컨테이너와 스프링 빈

### 스프링 컨테이너 생성

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
```

- `ApplicationContext`를 스프링 컨테이너라고 한다.
- `ApplicationContext`는 인터페이스이다.
- 스프링 컨테이너는 XML을 기반으로 만들 수 있고, annotation 기반의 자바 설정 클래스로 만들 수 있다.
- 직전에 `AppConfig`를 사용했던 방식이 annotation 기반의 자바 설정 클래스로 스프링 컨테이너를 만든 것이다.
- 자바 설정 클래스를 기반으로 스프링 컨테이너 `ApplicationContext`를 만들어 보자.
  - `new AnnotationConfigApplicationContext(AppConfig.class);`
  - 이 클래스는 `ApplicationContext` 인터페이스의 구현체이다.



> 참고) 더 정확히는 스프링 컨테이너를 부를 때 `BeanFactory`, `ApplicationContext`로 구분해서 이야기한다. 이 부분은 뒤에서 설명하겠다. `BeanFactory`를 직접 사용하는 경우는 거의 없으므로 일반적으로 `ApplicationContext`를 스프링 컨테이너라 한다.



#### 스프링 컨테이너 생성 과정

**1. 스프링 컨테이너 생성**
![스프링 컨테이너 생성](./assets/스프링_컨테이너_생성.jpg)

- `new AnnotationConfigApplicationContext(AppConfig.class)`
- 스프링 컨테이너를 생성할 때는 구성 정보를 지정해주어야 한다.
- 여기서는 `AppConfig.class`를 구성 정보로 지정했다.

**2. 스프링 빈 등록**
![스프링 빈 등록](./assets/스프링_빈_등록.jpg)

- 스프링 컨테이너는 파라미터로 넘어온 설정 클래스 정보를 사용해서 스프링 빈을 등록한다.

**빈 이름**

- 빈 이름은 메서드 이름을 사용한다.
- 빈 이름을 직접 부여할 수도 있다.
  - `@Bean(name="memberService2")`

> 주의) **빈 이름은 항상 다른 이름을 부여**해야 한다. 같은 이름을 부여하면, 다른 빈이 무시되거나 기존 빈을 덮어버리거나 설정에 따라 오류가 발생한다.



**3. 스프링 빈 의존 관계 설정 - 준비**
![스프링 빈 의존 관계 설정 - 준비](./assets/스프링_빈_의존_관계_설정[준비].jpg)



**4. 스프링 빈 의존 관계 설정 - 완료**
![스프링 빈 의존 관계 설정 - 완료](./assets/스프링_빈_의존_관계_설정[완료].jpg)

- 스프링 컨테이너는 설정 정보를 참고해서 의존 관계를 주입(DI)한다.
- 단순히 자바 코드를 호출하는 것 같지만, 차이가 있다. 이 차이는 뒤에 싱글톤 컨테이너에서 설명한다.



**참고**
스프링은 빈을 생성하고, 의존 관계를 주입하는 단계가 나누어져 있다. 그런데 이렇게 자바 코드로 스프링 빈을 등록하면 생성자를 호출하면서 의존 관계 주입도 한번에 처리된다. 여기서는 이해를 돕기 위해 개념적으로 나누어 설명했다. 자세한 내용은 의존 관계 자동 주입에서 다시 설명하겠다.



**정리**
스프링 컨테이너를 생성하고, 설정(구성) 정보를 참고해서 스프링 빈도 등록하고, 의존 관계도 설정했다. 이제 스프링 컨테이너에서 데이터를 조회해보자.



### 컨테이너에 등록된 모든 빈 조회

스프링 컨테이너에 실제 스프링 빈들이 잘 등록 되었는지 확인해보자.

```java
class ApplicationContextInfoTest {
	AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
	@DisplayName("모든 빈 출력하기")
	void findAllBean() {
		String[] beanDefinitionNames = ac.getBeanDefinitionNames();
		for (String beanDefinitionName : beanDefinitionNames) {
			Object bean = ac.getBean(beanDefinitionName);
			System.out.println("name=" + beanDefinitionName + " object=" + bean);
		}
	}
    
	@Test
	@DisplayName("애플리케이션 빈 출력하기")
	void findApplicationBean() {
		String[] beanDefinitionNames = ac.getBeanDefinitionNames();
		for (String beanDefinitionName : beanDefinitionNames) {
			BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            //Role ROLE_APPLICATION: 직접 등록한 애플리케이션 빈
			//Role ROLE_INFRASTRUCTURE: 스프링이 내부에서 사용하는 빈
			if (beanDefinition.getRole() == BeanDefinition.ROLE_APPLICATION) {
                Object bean = ac.getBean(beanDefinitionName);
				System.out.println("name=" + beanDefinitionName + " object=" +
bean);
			}
		}
	}
}
```

- 모든 빈 출력하기
  - 실행하면 스프링에 등록된 모든 빈 정보를 출력할 수 있다.
  - `ac.getBeanDefinitionNames()` : 스프링에 등록된 모든 빈 이름을 조회한다.
  - `ac.getBean()` : 빈 이름으로 빈 객체(인스턴스)를 조회한다.
- 애플리케이션 빈 출력하기
  - 스프링이 내부에서 사용하는 빈은 제외하고, 내가 등록한 빈만 출력해보자.
  - 스프링이 내부에서 사용하는 빈은 `getRole()`으로 구분할 수 있다.
    - `ROLE_APPLICATION` : 일반적으로 사용자가 정의한 빈
    - `ROLE_INFRASTRUCTURE` : 스프링이 내부에서 사용하는 빈



### 스프링 빈 조회 - 기본

스프링 컨테이너에서 스프링 빈을 찾는 가장 기본적인 조회 방법

- `ac.getBean(빈이름, 타입)`
- `ac.getBean(타입)`
- 조회 대상 스프링 빈이 없으면 예외 발생
  - `NoSuchBeanDefinitionException: No bean named 'xxxxx' available`

> 참고) 구체 타입으로 조회하면 변경시 유연성이 떨어진다.



### 스프링 빈 조회 - 동일한 타입이 둘 이상

- 타입으로 조회 시 같은 타입의 스프링 빈이 둘 이상이면 오류가 발생한다. 이때는 빈 이름을 지정하자.
- `ac.getBeansOfType()`을 사용하면 해당 타입의 모든 빈을 조회할 수 있다.



### 스프링 빈 조회 - 상속 관계

- 부모 타입으로 조회하면, 자식 타입도 함께 조회한다.
- 그래서 모든 자바 객체의 최고 부모인 `Object`타입으로 조회하면, 모든 스프링 빈을 조회한다.

![스프링 빈 조회 - 상속 관계](./assets/스프링_빈_조회_상속관계.jpg)



### BeanFactory와 ApplicationContext

beanFactory와 ApplicationContext에 대해서 알아보자.

![BeanFactory와 ApplicationContext](./assets/BeanFactory와_ApplicationContext.jpg)

**BeanFactory**

- 스프링 컨테이너의 최상위 인터페이스이다.
- **스프링 빈을 관리하고 조회하는 역할**을 담당한다.
- `getBean()`을 제공한다.
- 지금까지 우리가 사용했던 대부분의 기능은 BeanFactory가 제공하는 기능이다.

**ApplicationContext**

- BeanFactory 기능을 모두 상속받아서 제공한다.
- 빈을 관리하고 검색하는 기능을 BeanFactory가 제공해주는데, 그러면 둘의 차이는 뭘까?
- 애플리케이션을 개발할 때 빈을 관리하고 조회하는 기능은 물론이고, 수많은 부가 기능이 필요하다.

**ApplicationContext가 제공하는 부가 기능**
![ApplicationContext가 제공하는 부가 기능](./assets/ApplicationContext가_제공하는_부가_기능.jpg)

- **메시지 소스를 활용한 국제화 기능(MessageSource)**
  - 예를 들어, 한국에서 들어오면 한국어로, 영어권에서 들어오면 영어로 출력
- **환경 변수(EnvironmentCapable)**
  - 로컬, 개발, 운영(스테이징) 등을 구분해서 처리
- **애플리케이션 이벤트(ApplicationEventPublisher)**
  - 이벤트를 발행하고 구독하는 모델을 편리하게 지원
- **편리한 리소스 조회(ResourceLoader)**
  - 파일, 클래스패스, 외부 등에서 리소스를 편리하게 조회



**정리**

- ApplicationContext는 BeanFactory의 기능을 상속받는다.
- ApplicationContext는 빈 관리 기능 + 편리한 부가 기능을 제공한다.
- BeanFactory를 직접 사용할 일은 거의 없다. 부가 기능이 포함된 ApplicationContext를 사용한다.
- BeanFactory나 ApplicationContext를 스프링 컨테이너라고 한다.



### 다양한 설정 형식 지원 - 자바 코드, XML

- 스프링 컨테이너는 다양한 형식의 설정 정보를 받아드릴 수 있게 유연하게 설계되어 있다.

- 자바 코드, XML, Groovy 등

  ![다양한 설정 형식 지원](./assets/다양한_설정_형식_지원.jpg)



**애노테이션 기반 자바 코드 설정 사용**

- 지금까지 했던 것이다.
- `new AnnotationConfigApplicationContext(AppConfig.class)`
- `AnnotationConfigApplicationContext` 클래스를 사용하면서 자바 코드로 된 설정 정보를 넘기면 된다.



**XML 설정 사용**

- 최근에는 스프링 부트를 많이 사용하면서 XML기반의 설정은 잘 사용하지 않는다. 아직 많은 레거시 프로젝트들이 XML로 되어 있고, 또 XML을 사용하면 컴파일 없이 빈 설정 정보를 변경할 수 있는 장점도 있으므로 한번쯤 배워두는 것도 괜찮다.
- `GenericXmlApplicationContext`를 사용하면서 `xml` 설정 파일을 넘기면 된다.
- xml 기반의 `appConfig.xml` 스프링 설정 정보와 자바 코드로 된 `AppConfig.java` 설정 정보를 비교해보면 거의 비슷하다는 것을 알 수 있다.
- xml 기발으로 설정하는 것은 최근에 잘 사용하지 않으므로 이정도로 마무리하고, 필요하면 스프링 공식 레퍼런스 문서를 확인하자.
  - https://spring.io/projects/spring-framework



### 스프링 빈 설정 메타 정보 - BeanDefinition

- 스프링은 어떻게 이런 다양한 설정 형식을 지원하는 것일까? 그 중심에는 `BeanDefinition`이라는 추상화가 있다.

- 쉽게 이야기해서 **역할과 구현을 개념적으로 나눈 것**이다!

  - XML을 읽어서 BeanDefinition을 만들면 된다.
  - 자바 코드를 읽어서 BeanDefinition을 만들면 된다.
  - 스프링 컨테이너는 자바 코드인지, XML인지 몰라도 된다. 오직 BeanDefinition만 알면 된다.

- `BeanDefinition`을 빈 설정 메타 정보라고 한다.

  - `@Bean`, `<bean>` 당 각각 하나씩 메타 정보가 생성된다.

- 스프링 컨테이너는 이 메타 정보를 기반으로 스프링 빈을 생성한다.

  ![BeanDefinition](./assets/BeanDefinition.jpg)



**코드 레벨로 조금 더 깊이 있게 들어가보자.**

![BeanDefinition code level](./assets/BeanDefinition_code_level.jpg)

- `AnnotationConfigApplicationContext`는 `AnnotatedBeanDefinitionReader`를 사용해서 `AppConfig.class`를 읽고 `BeanDefinition`을 생성한다.
- `GenericXmlApplicationContext`는 `XmlBeanDefinitionReader`를 사용해서 `appConfig.xml` 설정 정보를 읽고 `BeanDefinition`을 생성한다.
- 새로운 형식의 설정 정보가 추가되면, XxxBeanDefinitionReader를 만들어서 `BeanDefinition`을 생성하면 된다.



*BeanDefinition 살펴보기*

**BeanDefinition 정보**

- BeanClassName: 생성할 빈의 클래스 명(자바 설정처럼 팩토리 역할의 빈을 사용하면 없음)
- factoryBeanName: 팩토리 역할의 빈을 사용할 경우 이름. 예) appConfig
- factoryMethodName: 빈을 생성할 팩토리 메서드 지정. 예) memberSerivce
- Scope: 싱글톤(기본값)
- lazyInit: 스프링 컨테이너를 생성할 때 빈을 생성하는 것이 아니라, 실제 빈을 사용할 때까지 최대한 생성을 지연처리 하는지 여부
- InitMethodName: 빈을 생성하고, 의존 관계를 적용한 뒤에 호출되는 초기화 메서드 명
- DestroyMethodName: 빈의 생명주기가 끝나서 제거하기 직전에 호출되는 메서드 명
- Constructor arguments, Properties: 의존 관계 주입에서 사용한다. (자바 설정처럼 팩토리 역할의 빈을 사용하면 없음)



**정리**

- BeanDefinition을 직접 생성해서 스프링 컨테이너에 등록할 수도 있다. 하지만 실무에서 BeanDefinition을 직접 정의하거나 사용할 일은 거의 없다.
- BeanDefinition에 대해서는 너무 깊이있게 이해하기 보다는, 스프링이 다양한 형태의 설정 정보를 BeanDefinition으로 추상화해서 사용하는 것 정도만 이해하면 된다.
- 가끔 스프링 코드나 스프링 관련 오픈 소스의 코드를 볼 때, BeanDefinition이라는 것이 보일 때가 있다. 이때 이러한 메커니즘을 떠올리면 된다.



## 4. 싱글톤 컨테이너

### 웹 애플리케이션과 싱글톤

- 스프링은 태생이 기업용 온라인 서비스 기술을 지원하기 위해 탄생했다.

- 대부분의 스프링 애플리케이션은 웹 애플리케이션이다. 물론 웹이 아닌 애플리케이션도 얼마든지 개발할 수 있다.

- 웹 애플리케이션은 보통 여러 고객이 동시에 요청한다.

  ![웹 애플리케이션과 싱글톤](./assets/웹_애플리케이션과_싱글톤.jpg)

- 우리가 만들었던 스프링 없는 순수한 DI 컨테이너인 AppConfig는 요청할 때마다 객체를 새로 생성한다.
- 고객 트래픽이 초당 100이 나오면 초당 100개 객체가 생성되고 소멸된다! ➡️ 메모리 낭비
- 해결 방안은 해당 객체가 딱 1개만 생성되고, 공유하도록 설계하면 된다. ➡️ **싱글톤 패턴**



### 싱글톤 패턴

- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴이다.
- 그래서 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야 한다.
  - private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용하지 못하도록 막아야 한다.



**싱글톤 적용**

```java
public class SingletonService {
    
    // 1. static 영역에 객체를 딱 1개만 생성해둔다.
    private static final StringletonService instance = new SingletonService();
    
    // 2. public으로 열어서 객체 인스턴스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용한다.
    public static SingletonService getInstance() {
        return instance;
    }
    
    public SingletonService() {}
    
    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
}
```

1. static 영역에 객체 instance를 미리 하나 생성해서 올려둔다.
2. 이 객체 인스턴스가 필요하면 오직 `getInstance()` 메서드를 통해서만 조회할 수 있다. 이 메서드를 호출하면 항상 같은 인스턴스를 반환한다.
3. 딱 1개의 객체 인스턴스만 존재해야 하므로, 생성자를 private으로 막아서 혹시라도 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막는다.



**싱글톤 테스트**

```java
@Test
@DisplayName("싱글톤 패턴을 적용한 객체 사용")
void singletonServiceTest() {
    
    // private으로 생성자를 막아두었다. 컴파일 오류가 발생한다.
    // new SingletonSerivce();
    
    // 1. 조회: 호출할 때마다 같은 객체를 반환
    SingletonService singletonService1 = SingletonService.getInstance();
    
    // 2. 조회: 호출할 때마다 같은 객체를 반환
    SingletonService singletonService2 = SingletonService.getInstance();
    
    // 참조값이 같은 것을 확인
    System.out.println("singletonService1 = " + singletonService1);
    System.out.println("singletonService2 = " + singletonService2);
    
    // singletonService1 == singletonService2
    assertThat(singletonService1).isSameAs(singletonService2);
    
    singletonService1.logic();
}
```

- private으로 new 키워드를 막아두었다.
- 호출할 때마다 같은 객체 인스턴스를 반환하는 것을 확인할 수 있다.

> 참고: 싱글톤 패턴을 구현하는 방법은 여러가지가 있다. 여기서는 객체를 미리 생성해두는 가장 단순하고 안전한 방법을 선택했다.



싱글톤 패턴을 적용하면 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 사용할 수 있다. 하지만 싱글톤 패턴은 다음과 같은 수많은 문제점들을 가지고 있다.

**싱글톤 패턴 문제점**

- 싱글톤 패턴은 구현하는 코드 자체가 많이 들어간다.
- 의존 관계상 클라이언트가 구체 클래스에 의존한다 ➡️ DIP를 위반한다.
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높다.
- 테스트하기 어렵다.
- 내부 속성을 변경하거나 초기화 하기 어렵다.
- private 생성자로 자식 클래스를 만들기 어렵다.
- 결론적으로 유연성이 떨어진다.
- 안티패턴으로 불리기도 한다.



### 싱글톤 컨테이너

스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서, 객체 인스턴스를 싱글톤(1개만 생성)으로 관리한다. 지금까지 우리가 학습한 스프링 빈이 바로 싱글톤으로 관리되는 빈이다.



**싱글톤 컨테이너**

- 스프링 컨테이너는 싱글톤 패턴을 적용하지 않아도 객체 인스턴스를 싱글톤으로 관리한다.

  - 이전에 설명한 컨테이너 생성 과정을 자세히 보자. 컨테이너는 객체를 하나만 생성해서 관리한다.

- 스프링 컨테이너는 싱글톤 컨테이너 역할을 한다. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 **싱글톤 레지스트리**라 한다.

- 스프링 컨테이너의 이런 기능 덕분에 싱글톤 패턴의 모든 단점을 해결하면서 객체를 싱글톤으로 유지할 수 있다.

  - 싱글톤 패턴을 위한 지저분한 코드가 들어가지 않아도 된다.
  - DIP, OCP, 테스트, private 생성자로부터 자유롭게 싱글톤을 사용할 수 있다.

  ```java
  @Test
  @DisplayName("스프링 컨테이너와 싱글톤")
  void springContainer() {
      
      ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
      
      // 1. 조회: 호출할 때마다 같은 객체를 반환
      MemberService memberService1 = ac.getBean("memberService", MemberService.class);
      
      // 2. 조회: 호출할 때마다 같은 객체를 반환
      MemberService memberService2 = ac.getBean("memberService", MemberService.class);
      
      // 참조값이 같은 것을 확인
      System.out.println("memberService1 = " + memberService1);
      System.out.println("memberService2 = " + memberService2);
      
      // memberSerivce1 == memberService2
      assertThat(memberService1).isSameAs(memberService2);
  }
  ```



**싱글톤 컨테이너 적용 후**

![싱글톤 컨테이너 적용 후](./assets/싱글톤_컨테이너_적용_후.jpg)

- 스프링 컨테이너 덕분에 고객의 요청이 올 때마다 객체를 생성하는 것이 아니라, 이미 만들어진 객체를 공유해서 효율적으로 재사용할 수 있다.



> 참고: 스프링의 기본 빈 등록 방식은 싱글톤이지만, 싱글톤 방식만 지원하는 것은 아니다. 요청할 때마다 새로운 객체를 생성해서 반환하는 기능도 제공한다. 자세한 내용은 빈 스코프에서 설명하겠다.



### 싱글톤 방식의 주의점

- 싱글톤 패턴이든, 스프링 같은 싱글톤 컨테이너를 사용하든, 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지(stateful)하게 설계하면 안된다.
- 무상태(stateless)로 설계해야 한다.
  - 특정 클라이언트에 의존적인 필드가 있으면 안된다.
  - 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안된다.
  - 가급적 읽기만 가능해야 한다.
  - 필드 대신에 자바에서 공유되지 않는 지역 변수, 매개 변수, ThreadLocal 등을 사용해야 한다.
- 스프링 빈의 필드에 공유 값을 설정하면 정말 큰 장애가 발생할 수 있다!!!



**상태를 유지할 경우 발생하는 문제점 예시1**

```java
public class StatefulService {
    
    private int price;
    
    public void order(String name, int price) {
        
        System.out.println("name = " + name + " price = " + price);
        this.price = price; // 여기가 문제!
    }
    
    public int getPrice() { return price; }
}
```



**상태를 유지할 경우 발생하는 문제점 예시2**

```java
public class StatefulServiceTest {
    
    @Test
    void statefulServiceSingleton() {
        
        ApplicationContext ac = new AnnotationConfigApplicationContext(TestConfig.class);
        
        StatefulService statefulService1 = ac.getBean("statefulService", StatefulService.class);
        StatefulService statefulService2 = ac.getBean("statefulService", StatefulService.class);
        
        // ThreadA: 사용자 A가 10,000원 주문
        statefulService1.order("userA", 10000);
        // ThreadB: 사용자 B가 20,000원 주문
        statefulService2.order("userB", 20000);
        
        // ThreadA: 사용자 A 주문 금액 조회
        int price = statefulService1.getPrice();
        // ThreadA: 사용자 A는 10,000원을 기대했지만, 기대와 다르게 20,000원을 출력
        System.out.println("price = " + price);
        
        assertThat(statefulService1.getPrice()).isEqualTo(20000);
    }
    
    static class TestConfig {
        
        @Bean
        public StatefulService statefulService() {
            return new StatefulService();
        }
    }
}
```

- 최대한 단순히 설명하기 위해, 실제 Thread는 사용하지 않았다.
- TreadA가 사용자 A 코드를 호출하고, ThreadB가 사용자 B 코드를 호출한다고 가정하자.
- `StatefulService`의 `price` 필드는 공유되는 필드인데, 특정 클라이언트가 값을 변경한다.
- 사용자 A의 주문 금액은 10,000원이 되어야 하는데, 20,000원이라는 결과가 나왔다.
- 실무에서 이런 경우를 종종 보는데, 이로 인해 정말 해결하기 어려운 큰 문제들이 터진다. (몇년에 한번씩 꼭 만난다.)
- 진짜 공유 필드는 조심해야 한다! 스프링 빈은 항상 무상태(stateless)로 설계하자.



### @Configuration과 싱글톤

그런데 이상한 점이 있다. 다음 AppConfig 코드를 보자.

```java
@Configuration
public class AppConfig {
    
    @Bean
    public MemberService memberService() {
        return new MemberServiceImpl(memberRepository());
    }
    
    @Bean
    public OrderService orderService() {
        return new OrderServiceImpl(
        		memberRepository(),
        		discountPolicy());
    }
    
    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
    
    ...
}
```

- memberService 빈을 만드는 코드를 보면 `memberRepository()`를 호출한다.

  - 이 메서드를 호출하면 `new MemoryMemberRepository()`를 호출한다.

- orderService 빈을 만드는 코드도 동일하게 `memberRepository()`를 호출한다.

  - 이 메서드를 호출하면 `new MemoryMemberRepository()`를 호출한다.


  
  결과적으로 각각 다른 2개의 `MemoryMemberRepository()`가 생성되면서 싱글톤이 깨지는 것처럼 보인다. 스프링 컨테이너는 이 문제를 어떻게 해결할까?



**검증 용도의 코드 추가**

```java
public class MemberServiceImpl implements MemberService {
    
    private final MemberRepository memberRepository;
    
    // 테스트 용도
    public MemberRepository getMemberRepository() {
        return memberRepository;
    }
}

public class OrderServiceImple implements OrderService {
    
    private final MemberRepository memberRepository;
    
    // 테스트 용도
    public MemberRepository getMemberRepository() {
        return memberRepository;
    }
}
```

- 테스트를 위해 MemberRepository를 조회할 수 있는 기능을 추가한다. 기능 검증을 위해 잠깐 사용하는 것이니 인터페이스에 조회 기능까지 추가하지는 말자.



**테스트 코드**

```java
public class ConfigurationSingletonTest {
    
    @Test
    void configurationTest() {
        
        ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
        
        MemberServiceImpl memberService = ac.getBean("memberService", MemberServiceImpl.class);
        OrderServiceImpl orderService = ac.getBean("orderService", OrderServiceImpl.class);
        MemberRepository memberRepository = ac.getBean("memberRepository", MemberRepository.class);
        
        // 모두 같은 인스턴스를 참조하고 있다.
        System.out.println("memberSerivce -> memberRepository = " + memberService.getMemberRepository());
        System.out.println("orderSerivce -> memberRepository = " + orderService.getMemberRepository());
        System.out.println("memberRepository = " + memberRepository);
        
        // 모두 같은 인스턴스를 참조하고 있다.
        assertThat(memberSerivce.getMemberRepository()).isSameAs(memberRepository);
        assertThat(orderSerivce.getMemberRepository()).isSameAs(memberRepository);
    }
}
```

- 확인해보면 memberRepository 인스턴스는 모두 같은 인스턴스가 공유되어 사용된다.
- AppConfig의 자바 코드를 보면 분명히 각각 2번 `new MemoryMemberRepository`를 호출해서 다른 인스턴스가 생성되어야 하는데..?
- 어떻게 된 일일까? 혹시 두 번 호출이 안되는 것일까? 실험을 통해 알아보자.



**AppConfig에 호출 로그 남김**

```java
@Configuration
public class AppConfig {
    
    @Bean
    public MemberService memberService() {
        // 1번
        System.out.println("call AppConfig.memberService");
        return new MemberServiceImpl(memberRepository());
    }
    
    @Bean
    public OrderService orderService() {
        // 1번
        System.out.println("call AppConfig.orderService");
        return new OrderServiceImpl(
        		memberRepository(),
        		discountPolicy());
    }
    
    @Bean
    public MemberRepository memberRepository() {
        // 2번? 3번?
        System.out.println("call AppConfig.memberRepository");
        return new MemoryMemberRepository();
    }
    
    ...
}
```

스프링 컨테이너가 각각 @Bean을 호출해서 스프링 빈을 생성한다. 그래서 `memberRepository()`는 다음과 같이 총 3번이 호출되어야 하는 것 아닐까?

1. 스프링 컨테이너가 스프링 빈에 등록하기 위해 @Bean이 붙어있는 `memberRepository()` 호출
2. memberService() 로직에서 `memberRepository()` 호출
3. orderService() 로직에서 `memberRepository()` 호출

그런데 출력 결과는 모두 한번만 호출된다.

```markdown
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
```



### @Configuration과 바이트코드 조작의 마법

스프링 컨테이너는 싱글톤 레지스트리다. 따라서 스프링 빈이 싱글톤이 되도록 보장해주어야 한다. 그런데 스프링이 자바 코드까지 어떻게 하기는 어렵다. 저 자바 코드를 보면 분명 3번 호출되어야 하는 것이 맞다. 그래서 스프링은 클래스의 바이트코드를 조작하는 라이브러리를 사용한다.
모든 비밀은 `@Configuration`을 적용한 `AppConfig`에 있다.



**다음 코드를 보자**

```java
@Test
void configurationDeep() {
    ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
    
    //AppConfig도 스프링 빈으로 등록된다.
    AppConfig bean = ac.getBean(AppConfig.class);
    
    System.out.println("bean = " + bean.getClass());
    //출력: bean = class hello.core.AppConfig$$EnhancerBySpringCGLIB$$d36e7d6f
}
```

- 사실 `AnnotationConfigApplicationContext`에 파라미터로 넘긴 값은 스프링 빈으로 등록된다. 그래서 `AppConfig`도 스프링 빈이 된다.

- `AppConfig` 스프링 빈을 조회해서 클래스 정보를 출력해보자.

  ```markdown
  bean = class hello.core.AppConfig
  ```

  순수한 클래스라면 다음과 같이 출력되어야 한다.

  `class hello.core.AppConfig`

  그런데 예상과는 다르게 클래스 명에 xxxCGLIB가 붙으면서 상당히 복잡해진 것을 볼 수 있다. 이것은 내가 만든 클래스가 아니라 스프링이 CGLIB라는 바이트코드 조작 라이브러리를 사용해서 AppConfig 클래스를 상속받은 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록한 것이다.



**그림**

![CGLIB를 사용한 AppConfig](./assets/CGLIB를_사용한_AppConfig.jpg)

그 임의의 다른 클래스가 바로 싱글톤이 보장되도록 해준다. 아마도 다음과 같이 바이트코드를 조작해서 작성되어 있을 것이다.(실제로는 CGLIB 내부 기술을 사용하는데, 매우 복잡하다.)



**AppConfig@CGLIB 예상 코드**

```java
@Bean
public MemberRepository memberRepository() {
    
    if(memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면) {
        return 스프링 컨테이너에서 찾아서 반환;
    }else {
        //스프링 컨테이너에 없으면
        기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
        return 반환;
    }
}
```

- @Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고, 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어진다.
- 덕분에 싱글톤이 보장되는 것이다.



> 참고: AppConfig@CGLIB는 AppConfig의 자식 타입이므로, AppConfig 타입으로 조회할 수 있다.



#### `@Configuration`을 적용하지 않고, `@Bean`만 적용하면 어떻게 될까?

`@Configuration`을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해서 싱글톤을 보장하지만, 만약 @Bean만 적용하면 어떻게 될까?

```markdown
bean = class hello.core.AppConfig
```

이 출력 결과를 통해서 AppConfig가 CGLIB 기술 없이 순수한 AppConfig로 스프링 빈에 등록된 것을 확인할 수 있다.

```markdown
call AppConfig.memberService
call AppConfig.memberRepository
call AppConfig.orderService
call AppConfig.memberRepository
call AppConfig.memberRepository
```

이 출력 결과를 통해서 MemberRepository가 총 3번 호출된 것을 알 수 있다. 1번은 @Bean에 의해 스프링 컨테이너에 등록하기 위해서이고, 2번은 각각 `memberRepository()`를 호출하면서 발생한 코드이다.



**인스턴스가 동일한지 테스트 결과**

```markdown
memberService → memberRepository = hello.core.member.MemoryMemberRepository@3246fb96
orderService → memberRepository = hello.core.member.MemoryMemberRepository@2e222612
memberRepository = hello.core.member.MemoryMemberRepository@61386958
```

당연히 인스턴스가 같은지 테스트하는 코드도 실패하고, 각각 다른 MemoryMemberRepository 인스턴스를 가지고 있다.



**정리**

- @Bean만 사용해도 스프링 빈으로 등록되지만, 싱글톤을 보장하지 않는다.
  - `memberRepository()`처럼 의존관계 주입이 필요해서 메서드를 직접 호출할 때 싱글톤을 보장하지 않는다.
- 크게 고민할 것이 없다. 스프링 설정 정보는 항상 `@Configuration`을 사용하자.





## 5. 컴포넌트 스캔

### 컴포넌트 스캔과 의존관계 자동 주입 시작하기

- 지금까지 스프링 빈을 등록할 때는 자바 코드의 @Bean이나 XML의 <bean> 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다.
- 예제에서는 몇 개가 안됐지만, 이렇게 등록해야 할 스프링 빈이 수십, 수백 개가 되면 일일이 등록하기도 귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생한다. 역시 개발자는 반복을 싫어한다. (무엇보다 귀찮다)
- 그래서 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔이라는 기능을 제공한다.
- 또 의존관계도 자동으로 주입하는 `@Autowired`라는 기능도 제공한다.



코드로 컴포넌트 스캔과 의존관계 자동 주입을 알아보자.

먼저 기존 AppConfig.java는 과거 코드와 테스트를 유지하기 위해 남겨두고, 새로운 AutoAppConfig.java를 만들자.



**AutoAppConfig.java**

```java
@Configuration
@ComponentScan(excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = Configuration.class))
public class AutoAppConfig {
    
}
```

- 컴포넌트 스캔을 사용하려면 먼저 `ComponentScan`을 설정 정보에 붙여주면 된다.
- 기존의 AppConfig와는 다르게 `@Bean`으로 등록한 클래스가 하나도 없다!



> 참고: 컴포넌트 스캔을 사용하면 `@Configuration`이 붙은 설정 정보도 자동으로 등록되기 때문에, AppConfig, TestConfig 등 앞서 만들어두었던 설정 정보도 함께 등록되고, 실행되어 버린다. 그래서 `excludeFilters`를 이용해서 설정 정보는 컴포넌트 스캔 대상에서 제외했다. 보통 설정 정보를 컴포넌트 스캔 대상에서 제외하지는 않지만, 기존 예제 코드를 최대한 남기고 유지하기 위해서 이 방법을 선택했다.



컴포넌트 스캔은 이름 그대로 `@Component` 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. `@Component`를 붙여주자.



> 참고: `@Configuration`이 컴포넌트 스캔의 대상이 된 이유도 `@Configuration` 소스 코드를 열어보면 `@Component` 애노테이션이 붙어있기 때문이다.



이제 각 클래스가 컴포넌트 스캔의 대상이 되도록 `@Component` 애노테이션을 붙여주자.

- 이전에 AppConfig에서는 `@Bean`으로 직접 설정 정보를 작성했고, 의존관계도 직접 명시했다. 이제는 이런 설정 정보 자체가 없기 때문에, 의존관계 주입도 이 클래스 안에서 해결해야 한다.
- `@Autowired`는 의존관계를 자동으로 주입해준다. 자세한 룰은 조금 뒤에 설명하겠다.
- `@Autowired`를 사용하면 생성자에서 여러 의존관계도 한번에 주입받을 수 있다.
- `AnnotationConfigApplicationContext`를 사용하는 것은 기존과 동일하다.
- 설정 정보로 `AutoAppConfig` 클래스를 넘겨준다.
- 실행해보면 기존과 같이 잘 동작하는 것을 확인할 수 있다.



로그를 잘 보면 컴포넌트 스캔이 잘 동작하는 것을 확인할 수 있다.

```markdown
ClassPathBeanDefinitionScanner - Identified cadidate component class:
.. RateDiscountPolicy.class
.. MemberServiceImpl.class
.. MemoryMemberRepository.class
.. OrderServiceImpl.class
```



컴포넌트 스캔과 자동 의존관계 주입이 어떻게 동작하는지 그림으로 알아보자.

**1. @ComponentScan**
![@ComponentScan](./assets/@ComponentScan.jpg)

- `@ComponentScan`은 `@Component`가 붙은 모든 클래스를 스프링 빈으로 등록한다.
- 이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
  - **빈 이름 기본 전략: ** MemberServiceImpl 클래스 ➡️ memberServiceImpl
  - **빈 이름 직접 지정: ** 만약 스프링 빈의 이름을 직접 지정하고 싶으면 `@Component("memberService2")` 이런식으로 이름을 부여하면 된다.



**2. @Autowired 의존관계 자동 주입**
![@Autowired 의존관계 자동 주입](./assets/@Autowired_의존관계_자동_주입.jpg)

- 생성자에 `@Autowired`를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다.
- 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다.
  - `getBean(MemberRepository.class)`와 동일하다고 이해하면 된다.
  - 더 자세한 내용은 뒤에서 설명한다.

![@Autowired 의존관계 자동 주입-2](./assets/@Autowired_의존관계_자동_주입-2.jpg)

- 생성자에 파라미터가 많아도 다 찾아서 자동으로 주입한다.









