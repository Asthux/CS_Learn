# 디자인 패턴

---

디자인 패턴이란 프로그램을 설계할 때 발생했던 문제점들을 객체 간의 상호 관계 등을 이용하여 해결할 수 있도록 하나의 '규약' 형태로 만들어 놓은 것을 의미합니다.

### 1.1.1 싱글톤 패턴

싱글톤 패턴은 하나의 클래스에 오직 하나의 인스턴스만 가지는 패턴입니다. 하나의 클래스를 기반으로 여러 개의 개별적인 인스턴스를 만들 수 있지만, 그렇게 하지 않고 하나의 클래스를 기반으로 단 하나의 인스턴스를 만들어 이를 기반으로 로직을 만드는 데 쓰이며, 보통 데이터베이스 연결 모듈에 많이 사용합니다.

하나의 인스턴스를 만들어 놓고 해당 인스턴스를 다른 모듈들이 공유하며 사용하기 때문에 생성할 때 드는 비용이 줄어드는 장점이 있습니다. 하지만 의존성이 높아진다는 단점이 있습니다.

#### 자바에서의 싱글톤 패턴

```java
class Singleton {
		private static class sinleInstanceHolder {
				private static final Singleton INSTANCE = new Singleton();
		}
		public static Singleton getInstance() {
				return singleInstanceHolder.INSTANCE;
		}
}

public class HelloWorld {
  	public static void main(String[] args) {
      	Singleton a = Singleton.getInstance();
      	Singleton b = Singleton.getInstance();
      	if (a == b) {
          	System.out.println(true);
        }
    }
}

/**
* true
**/
```

#### 싱글톤 패턴의 단점

싱글톤 패턴은 TDD(Test Driven Development)를 할 때 걸림돌이 됩니다. TDD를 할 때 단위 테스트를 주로 하는데, 단위 테스트는 테스트가 서로 독립적이어야 하며 테스트를 어떤 순서로든 실행할 수 있어야 합니다. 하지만 싱글톤 패턴은 미리 생성된 하나의 인스턴스를 기반으로 구현하는 패턴이므로 각 테스트마다 '독립적인' 인스턴스를 만들기가 어렵습니다.

#### 의존성 주입

또한, 싱글톤 패턴은 사용하기가 쉽고 굉장히 실용적이지만 모듈 간의 결합을 강하게 만들 수 있다는 단점이 있습니다. 이 때 의존성 주입(DI, Dependency Injection)을 통해 모듈간의 결합을 조금 더 느슨하게 만들어 해결할 수 있습니다.

이 방식은 메인 모듈이 직접 다른 하위 모듈에 대한 의존성을 주기보다는 의존성 주입자(dependency injector)가 이 부분을 가로채 메인 모듈이 간접적으로 의존성을 주입하는 방식입니다. 이를 통해 메인 모듈은 하위 모듈에 대한 의존성이 떨어지게 됩니다. 참고로 이를 '디커플링이 된다'고도 합니다.

*장점* - 모듈들을 쉽게 교체할 수 있는 구조가 되어 테스팅하기 쉽고 마이그레이션 하기도 수월합니다. 또한, 구현할 때 추상화 레이어를 넣고 이를 기반으로 구현체를 넣어 주기 때문에 애플리케이션 의존성 방향이 일관되고, 애플리케이션을 쉽게 추론할 수 있으며, 모듈 간의 관계들이 조금 더 명확해집니다.

*단점* - 모듈들이 더욱더 분리되므로 클래스 수가 늘어나 복잡성이 증가될 수 있으며 약간의 런타임 페널티가 생기기도 합니다.

의존성 주입 원칙 - 의존성 주입은 "상위 모듈은 하위 모듈에서 어떠한 것도 가져오지 않아야 합니다. 또한, 둘 다 추상화에 의존해야 하며, 이때 추상화는 세부 사항에 의존하지 말아야 합니다."라는 의존성 주입 원칙을 지켜주면서 만들어야 합니다.

 

### 1.1.2 팩토리 패턴

팩토리 패턴은 객체를 사용하는 코드에서 객체 생성 부분을 떼어내 추상화한 패턴이자 상속 관계에 있는 두 클래스에서 상위 클래스가 중요한 뼈대를 결정하고, 하위 클래스에서 객체 생성에 관한 구체적인 내용을 결정하는 패턴입니다. 상위 클래스와 하위 클래스가 분리되기 때문에 느슨한 결합을 가지며 상위 클래스에서는 인스턴스 생성 방식에 대해 전혀 알 필요가 없기 때문에 더 많은 유연성을 갖게 됩니다. 그리고 객체 생성 로직이 따로 떼어져 있기 때문에 코드를 리팩터링하더라도 한 곳만 고칠 수 있게 되니 유지 보수성이 증가됩니다.

#### 자바의 팩토리 패턴

```java
enum CoffeeType {
  	LATTE,
  	ESPRESSO
}

abstract class Coffee {
  	protected String name;
  	
  	public String getName() {
      	return name;
    }
}

class Latte extends Coffee {
  	public Latte() {
      	name = "latte";
    }
}

class Espresso extends Coffee {
  	public Espresso() {
      	name = "Espresso";
    }
}

class CoffeFactory {
  	public static Coffee createCoffee(CoffeeType type) {
      	switch (type) {
	          case LATTE :
  	          	return new Latte();
	          case ESPRESSO :
            		return new Espresso();
            default :
            		throw new IllegalArgumentException("Invalid coffee type: " + type);
        }
    }
}

public class Main {
  	public static void main(String[] args) {
      	Coffee coffee = CoffeeFactory.createCoffee(CoffeeType.LATTE);
      	System.out.println(coffee.getName()); //latte
    }
}
```



### 1.1.3 전략 패턴

전략 패턴(strategy pattern)은 정책 패턴(policy pattern)이라고도 하며, 객체의 행위를 바꾸고 싶은 경우 직접 수정하지 않고 전략이라고 부르는 캡슐화한 알고리즘을 컨텍스트 안에서 바꿔주면서 상호 교체가 가능하게 만드는 패턴입니다.

우리가 어떤 것을 살 때 네이버페이, 카카오페이 등 다양한 방법으로 결제하듯이 어떤 아이템을 살 때 LUNACard로 사는 것과 KAKAOCard로 사는 것을 구현한 예제입니다. 결제 방식의 전략만 바꿔서 두 가지 방식으로 결제하는 것을 구현했습니다.

#### 자바의 전략 패턴

```java
import java.text.DecimalFormat;
import java.util.ArrayList;
import java.util.List;

interface PaymentStrategy { 
    public void pay(int amount);
} 

class KAKAOCardStrategy implements PaymentStrategy {
    private String name;
    private String cardNumber;
    private String cvv;
    private String dateOfExpiry;
    
    public KAKAOCardStrategy(String nm, String ccNum, String cvv, String expiryDate){
        this.name=nm;
        this.cardNumber=ccNum;
        this.cvv=cvv;
        this.dateOfExpiry=expiryDate;
    }

    @Override
    public void pay(int amount) {
        System.out.println(amount +" paid using KAKAOCard.");
    }
} 

class LUNACardStrategy implements PaymentStrategy {
    private String emailId;
    private String password;
    
    public LUNACardStrategy(String email, String pwd){
        this.emailId=email;
        this.password=pwd;
    }
    
    @Override
    public void pay(int amount) {
        System.out.println(amount + " paid using LUNACard.");
    }
} 

class Item { 
    private String name;
    private int price; 
    public Item(String name, int cost){
        this.name=name;
        this.price=cost;
    }

    public String getName() {
        return name;
    }

    public int getPrice() {
        return price;
    }
} 

class ShoppingCart { 
    List<Item> items;
    
    public ShoppingCart(){
        this.items=new ArrayList<Item>();
    }
    
    public void addItem(Item item){
        this.items.add(item);
    }
    
    public void removeItem(Item item){
        this.items.remove(item);
    }
    
    public int calculateTotal(){
        int sum = 0;
        for(Item item : items){
            sum += item.getPrice();
        }
        return sum;
    }
    
    public void pay(PaymentStrategy paymentMethod){
        int amount = calculateTotal();
        paymentMethod.pay(amount);
    }
}  

public class HelloWorld{
    public static void main(String []args){
        ShoppingCart cart = new ShoppingCart();
        
        Item A = new Item("kundolA",100);
        Item B = new Item("kundolB",300);
        
        cart.addItem(A);
        cart.addItem(B);
        
        // pay by LUNACard
        cart.pay(new LUNACardStrategy("kundol@example.com", "pukubababo"));
        // pay by KAKAOBank
        cart.pay(new KAKAOCardStrategy("Ju hongchul", "123456789", "123", "12/01"));
    }
}
/*
400 paid using LUNACard.
400 paid using KAKAOCard.
*/
```



### 1.1.4 옵저버 패턴

옵저버 패턴은 주체가 어떤 객체의 상태 변화를 관찰하다가 상태 변화가 있을 때마다 메서드 등을 통해 옵저버 목록에 있는 옵저버들에게 변화를 알려주는 패턴입니다. 여기서 주체란 객체의 상태 변화를 보고 있는 관찰자이며, 옵저버들이란 이 객체의 상태 변화에 따라 전달되는 메서드 등을 기반으로 '추가 변화 사항'이 생기는 객체들을 의미합니다. 또한, 주체와 객체를 따로 두지 않고 상태가 변경되는 객체를 기반으로 구축하기도 합니다.

옵저버 패턴을 활용한 서비스로는 트위터가 있습니다.

또한, 옵저버 패턴은 주로 이벤트 기반 시스템에 사용하며 MVC 패턴에도 사용됩니다. 예를 들어 주체라고 볼 수 있는 모델에서 변경사항이 생겨 update() 메서드로 옵저버인 뷰에 알려주고 이를 기반으로 컨트롤러 등이 작동하는 것이죠.

#### 자바에서의 옵저버 패턴

```java
import java.util.ArrayList;
import java.util.List;

interface Subject {
    public void register(Observer obj);
    public void unregister(Observer obj);
    public void notifyObservers();
    public Object getUpdate(Observer obj);
}

interface Observer {
    public void update(); 
}

class Topic implements Subject {
    private List<Observer> observers;
    private String message; 

    public Topic() {
        this.observers = new ArrayList<>();
        this.message = "";
    }

    @Override
    public void register(Observer obj) {
        if (!observers.contains(obj)) observers.add(obj); 
    }

    @Override
    public void unregister(Observer obj) {
        observers.remove(obj); 
    }

    @Override
    public void notifyObservers() {   
        this.observers.forEach(Observer::update); 
    }

    @Override
    public Object getUpdate(Observer obj) {
        return this.message;
    } 
    
    public void postMessage(String msg) {
        System.out.println("Message sended to Topic: " + msg);
        this.message = msg; 
        notifyObservers();
    }
}

class TopicSubscriber implements Observer {
    private String name;
    private Subject topic;

    public TopicSubscriber(String name, Subject topic) {
        this.name = name;
        this.topic = topic;
    }

    @Override
    public void update() {
        String msg = (String) topic.getUpdate(this); 
        System.out.println(name + ":: got message >> " + msg); 
    } 
}

public class HelloWorld { 
    public static void main(String[] args) {
        Topic topic = new Topic(); 
        Observer a = new TopicSubscriber("a", topic);
        Observer b = new TopicSubscriber("b", topic);
        Observer c = new TopicSubscriber("c", topic);
        topic.register(a);
        topic.register(b);
        topic.register(c); 
   
        topic.postMessage("amumu is op champion!!"); 
    }
}
/*
Message sended to Topic: amumu is op champion!!
a:: got message >> amumu is op champion!!
b:: got message >> amumu is op champion!!
c:: got message >> amumu is op champion!!
*/ 
```

topic을 기반으로 옵저버 패턴을 구현했습니다. 여기서 topic은 주체이자 객체가 됩니다. class Topic implements Subject를 통해 Subject interface를 구현했고 Observer a = new TopicSubscriber("a", topic); 으로 옵저버를 선언할 때 해당 이름과 어떠한 토픽의 옵저버가 될 것인지를 정했습니다.

#### 자바 : 상속과 구현

상속 (extends) - 상속은 자식 클래스가 부모 클래스의 메서드 등을 상속받아 사용하며 자식 클래스에서 추가 및 확장을 할 수 있는 것을 말합니다. 이로 인해 재사용성, 중복성의 최소화가 이루어집니다.

구현 (implements) - 구현은 부모 인터페이스를 자식 클래스에서 재정의하여 구현하는 것을 말하며, 상속과는 달리 반드시 부모 클래스의 메서드를 재정의하여 구현해야 합니다.

상속과 구현의 차이 - 상속은 일반 클래스, abstract 클래스를 기반으로 구현하며, 구현은 인터페이스를 기반으로 구현합니다.



### 1.1.5 프록시 패턴과 프록시 서버

#### 프록시 패턴

프록시 패턴은 대상 객체에 접근하기 전 그 접근에 대한 흐름을 가로채 해당 접근을 필터링하거나 수정하는 등의 역할을 하는 계층이 있는 디자인 패턴입니다.

이를 통해 객체의 속성, 변환 등을 보완하며 보안, 데이터 검증, 캐싱, 로깅에 사용합니다. 이는 앞서 설명한 프록시 객체로 쓰이기도 하지만 프록시 서버로도 활용됩니다.

- 프록시 서버에서의 캐싱

  캐시 안에 정보를 담아두고, 캐시 안에 있는 정보를 요구하는 요청에 대해 다시 저 멀리 있는 원격 서버에 요청하지 않고 캐시 안에 있는 데이터를 활용하는 것을 말한다. 이를 통해 불필요하게 외부와 연결하지 않기 때문에 트래픽을 줄일 수 있다는 장점이 있다.

#### 프록시 서버

프록시 서버는 서버와 클라이언트 사이에서 클라이언트가 자신을 통해 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 컴퓨터 시스템이나 응용 프로그램을 가리킵니다.

프록시 서버로 쓰는 CloudFlare

CloudFlare는 전 세계적으로 분산된 서버가 있고 이를 통해 어떠한 시스템의 콘텐츠 전달을 빠르게 할 수 있는 CDN 서비스입니다. CloudFlare는 웹 서버 앞단에 프록시 서버로 두어 DDOS 공격 방어나 HTTPS 구축에 쓰입니다. 또한, 서비스를 배포한 이후에 해외에서 무언가 의심스러운 트래픽이 많이 발생하면 이 때문에 많은 클라우드 서비스 비용이 발생할 수도 있는데, 이때 CloudFlare가 의심스러운 트래픽인지를 먼저 판단해 CAPTCHA 등을 기반으로 이를 일정 부분 막아주는 역할도 수행합니다.

- CDN

  각 사용자가 인터넷에 접속하는 곳과 가까운 곳에서 콘텐츠를 캐싱 또는 배포하는 서버 네트워크를 말한다. 이를 통해 사용자가 웹 서버로부터 콘텐츠를 다운로드하는 시간을 줄일 수 있다.

CORS와 프론트엔드의 프록시 서버

CORS(Cross-Origin Resource Sharing)는 서버가 웹 브라우저에서 리소스를 로드할 때 다른 오리진을 통해 로드하지 못하게 하는 HTTP 헤더 기반 메커니즘입니다. 프론트엔드 개발 시 프론트엔드 서버를 만들어서 백엔드 서버와 통신할 때 주로 CORS 에러를 마주치는데, 이를 해결하기 위해 프론트엔드에서 프록시 서버를 만들기도 합니다.



### 1.1.6 이터레이터 패턴

이터레이터 패턴은 이터레이터를 사용하여 컬렉션의 요소들에 접근하는 디자인 패턴입니다. 이를 통해 순회할 수 있는 여러 가지 자료형의 구조와는 상관없이 이터레이터라는 하나의 인터페이스로 순회가 가능합니다.



### 1.1.7 노출모듈 패턴

노출모듈 패턴은 즉시 실행 함수를 통해 private public 같은 접근 제어자를 만드는 패턴을 말합니다. 자바스크립트는 private나 public 같은 접근 제어자가 존재하지 않고 전역 범위에서 스크립트가 실행됩니다. 그렇기에 노출모듈 패턴을 통해 private와 public 접근 제어자를 구현하기도 합니다.



### 1.1.8 MVC 패턴

MVC 패턴은 모델, 뷰, 컨트롤러로 이루어진 디자인 패턴입니다. 애플리케이션의 구성 요소를 세 가지 역할로 구분하여 개발 프로세스에서 각각의 구성 요소에만 집중해서 개발할 수 있습니다. 재사용성과 확장성이 용이하다는 장점이 있고, 애플리케이션이 복잡해질수록 모델과 뷰의 관계가 복잡해지는 단점이 있습니다.

#### 모델

모델은 애플리케이션의 데이터인 데이터베이스, 상수, 변수 등을 뜻합니다. 뷰에서 데이터를 생성하거나 수정하면 컨트롤러를 통해 모델을 생성하거나 갱신합니다.

#### 뷰

뷰는 inputbox, checkbox, textarea 등 사용자 인터페이스 요소를 나타냅니다. 즉, 모델을 기반으로 사용자가 볼 수 있는 화면을 뜻합니다. 모델이 가지고 있는 정보를 따로 저장하지 않아야 하며 단순히 사각형 모양 등 화면에 표시하는 정보만 가지고 있어야 합니다. 또한 변경이 일어나면 컨트롤러에 이를 전달해야 합니다.

#### 컨트롤러

컨트롤러는 하나 이상의 모델과 하나 이상의 뷰를 잇는 다리 역할을 하며 이벤트 등 메인 로직을 담당합니다. 또한, 모델과 뷰의 생명주기도 관리하며, 모델이나 뷰의 변경 통지를 받으면 이를 해석하여 각각의 구성 요소에 해당 내용에 대해 알려줍니다.

#### MVC 패턴의 예 스프링

MVC 패턴을 이용한대표적인 프레임워크로는 자바 플랫폼을 위한 오픈 소스 애플리케이션 프레임워크인 스프링이 있습니다. 스프링의 WEB MVC는 웹 서비스를 구축하는 데 편리한 기능들을 많이 제공합니다. 예를 들어 @Requestparam, @RequestHeader, @PathVariable 등의 애너테이션을 기반으로 사용자의 요청 값들을 쉽게 분석할 수 있으며 사용자의 어떠한 요청이 유효한 요청인지를 쉽게 거를 수 있습니다. 예를 들어 숫자를 입력해야하는데, 문자를 입력하는 사례 같은 것 말이죠. 또한 재사용 가능한 코드, 테스트, 쉽게 리디렉션할 수 있게 하는 등의 장점이 있습니다.



### 1.1.9 MVP 패턴

MVP 패턴은 MVC 패턴으로부터 파생되었으며 MVC에서 C에 해당하는 컨트롤러가 프레젠터로 교체된 패턴입니다.  뷰와 프레젠터는 일대일 관계이기 때문에 MVC 패턴보다 더 강한 결합을 지닌 디자인 패턴이라고 볼 수 있습니다.



### 1.1.10 MVVM패턴

MVVM 패턴은 MVC의 C에 해당하는 컨트롤러가 뷰모델로 바뀐 패턴입니다. 여기서 뷰모델은 뷰를 더 추상화한 계층이며, MVVM 패턴은 MVC 패턴과는 다르게 커맨드와 데이터 바인딩을 가지는 것이 특징입니다. 뷰와 뷰모델 사이의 양방향 데이터 바인딩을 지원하며 UI를 별도의 수정 없이 재사용할 수 있고 단위 테스팅하기 쉽다는 장점이 있습니다.

#### MVVM 패턴의 예 : 뷰

MVVM 패턴을 가진 대표적인 프레임워크로는 뷰가 있습니다. Vue.js 는 반응형이 특징인 프론트엔드 프레임워크입니다. 예를 들어 watch 와 computed 등으로 쉽게 반응형적인 값들을 구축할 수 있습니다. 함수를 사용하지 않고 값 대입만으로 변수가 변경되며 양방향 바인딩, html을 토대로 컴포넌트를 구축할 수 있다는 점이 특징입니다. 재사용 가능한 컴포넌트 기반으로 UI를 구축할 수 있으며 BMW, 구글, 루이비통 등에서 사용합니다.