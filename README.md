# RxJava : ReactiveX를 Java에서 사용할수 있게 해주는 라이브러리

출처 : RxJava 프로그래밍 _ 한빛미디어

[참고](https://github.com/JihyunK/project_zzz_note/blob/master/study/RxJava.md)

## [ 순서 ]
> 
> [1. RxJava 란 ?](#1-rxjava-란-)  
>    - 컴퓨터 프로그램의 분류
>    - ReactiveX
>  
> [2. RxJava가 만들어진 이유](#2-rxjava가-만들어진-이유)
>   
> [3. 데이터 소스 클래스 ](#3-데이터-소스-클래스들)  
>    - [1. Observable 클래스 [ 차가운 옵저버블 , Cold Observable ]](#1-observable-클래스--차가운-observable-)
>    - [2. Single 클래스](#2-single-클래스)
>    - [3. Maybe 클래스](#3-maybe-클래스)
>    - [4. Flowable 클래스](#4-flowable-클래스)
> 
> [4. 뜨거운 옵저버블 [Hot Observable]](#4-뜨거운-옵저버블-hot-observable)  
>    - [1. AsyncSubject 클래스](#1-asyncsubject-클래스)
>    - [2. BehaviorSubject 클래스](#2-behaviorsubject-클래스)
>    - [3. PublishSubject 클래스](#3-publishsubject-클래스)
>    - [4. ReplaySubject 클래스](#4-replaysubject-클래스)
>    - [5. ConnectableObservable 클래스](#2-connectableobservable-클래스)
> 
> [5. Map(), filter(), reduce(), flatMap()함수의 사용법](#5-map-filter-reduce-flatmap함수의-사용법)  
>    - [1. map() 함수](#1-map-함수)
>    - [2. flatMap() 함수](#2-flatmap-함수)
>    - [3. filter() 함수](#3-filter-함수)
>    - [4. reduce() 함수](#4-reduce-함수)
> 
> [~~6. 생성 연산자, 결합 연산자, 변환 연산자 등 카테고리별 주요 함수~~](#6-생성-연산자-결합-연산자-변환-연산자-등-카테고리별-주요-함수)  
> 
> [7. 스케쥴러의 의미, subscribeOn()과 observeOn() 함수의 차이](#7-스케쥴러의-의미-subscribeon과-observeon-함수의-차이)  
>    - [1. 스케줄러란 ?](#1-스케줄러란-)
>    - [2. 스케줄러 종류](#2-스케줄러-종류)
>    - [3. subscribeOn()](#3-subscribeon)
>    - [4. observeOn()](#4-observeon)
> 
> [~~8. 그 밖의 디버깅, 흐름 제어 함수를 익힘~~](#8-그-밖의-디버깅-흐름-제어-함수를-익힘)  

----------------

## 1. RxJava 란 ?
> 먼저 컴퓨터 프로그램의 분류를 알아두면 리액티브 프로그램을 이해하는데에 도움이 된다.

- 컴퓨터 프로그램의 분류

    .1 `변환` 프로그램 : 주어진 입력값을 바탕으로 결과를 계산하는 프로그램.  
    - 일반적인 예는 컴파일러와 수치 계산 프로그램 이다.  

    .2 `상호작용` 프로그램 : 프로그램이 주도하는 속도로 사용자 혹은 다른 프로그램과 상호작용을 합니다.

    - 사용자의 관점으로 볼때 시분할 [time-sharing] 시스템은 상호작용 프로그램 입니다. 

    .3 `리엑티브` 프로그램 : 주변의 환경과 끊임 없는 상호작용을 하는데 프로그램이 주도하는 것이 아니라 <b>환경이 변하면 이벤트를 받아 동작</b>합니다.

    - 상호작용 프로그램과의 차이 ??
      
      > 상호작용 프로그램은 자신의 속도에 맞춰 일하고 대부분 통신을 담당한다.  
      > 반면에 리액티브 프로그램은 외부 요구의 반응에 맞춰 일하고 대부분 정확한 **인터럽트** 처리를 담당한다.
      > > -인터럽트 ??  
      > > 마이크로프로세서( CPU )가 프로그램을 실행하고 있을 때, 입출력 하드웨어 등의 장치나 또는 예외상황이 발생하여 처리가 필요할 경우에 CPU에게 알려 처리할 수 있도록 하는 것을 말한다  
      > > [출처](https://ko.wikipedia.org/wiki/%EC%9D%B8%ED%84%B0%EB%9F%BD%ED%8A%B8)

    > 보통 애플리케이션을 만드는 프로그래머가 작성하는 프로그램은 `변환 프로그램`이거나 `상호작용 프로그램`이다.  
    > 
    > `인터럽트` 같은 개념은 `시스템 프로그래머`가 담당한다.  
    > 혹은 클라이언트의 요청을 처리하는 `서버 프로그래밍`은 `리액티브 프로그래밍`에 가깝다.  
    > 
    > 애플리케이션에서 RxJava와 같은 리액티브 프로그래밍을 하려면 누군가 리액티브 프로그래밍을 할 수 있는 기반 시설을 제공해주어야 한다.  
    > 
    > 즉 , **데이터 소스를 정의할 수 있고, 그것의 변경 사항을 받아서 내 프로그램에 알려줄[push] 존재가 필요**한데, 그것을 .NET 환경에서는 리액티브 확장[ Reactive Extentions : ReactiveX ]이라고 하고, JVM 위의 자바 언어로 구현해놓은 라이브러리가 RxJava 입니다.  
    > 
    > <big>결론</big>  
    > RxJava는 자바 애플리케이션을 만드는 프로그래머가 리액티브 프로그램을 만들기 위해 만들어진 라이브러리이다.

- ReactiveX : [출처](http://atin.tistory.com/576)  
    > 관찰 가능한 시퀀스를 사용하여 비동기 및 이벤트 기반 프로그램을 작성하기 위한 라이브러리이다.  
    > 
    > 옵저버 패턴을 확장하여 데이터 또는 이벤트의 시퀀스를 지원하고 , 낮은 수준의 스레딩, 동기화, 스레드 같은 문제를 추상화하는 동시에 선언적으로 시퀀스를 조합 할 수 있는 연산자를 추가했다.  

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## 2. RxJava가 만들어진 이유
•	RxJava는 2013년에 넷플릭스의 기술 블로그에서 처음으로 소개되었다.
당시, 넷플릭스는 REST 기반의 서비스 API 호출 횟수와 서비스의 전반적인 성능을 개선하는 프로젝트를 진행했고, 그 결과 .NET 환경의 리액티브 확장 라이브러리(Rx)를 JVM에 포팅하여 RxJava를 만들었다.

넷플릭스에서는 RxJava를 만들게 된 핵심적인 이유를 다음과 같이 밝혔다.

    • 동시성을 적극적으로 끌어안을 필요가 있다.

    • 자바 Future[자바 5부터 제공]를 조합하기 어렵다는 점을 해결해야 한다.

    • 콜백 방식의 문제점을 개선해야 한다.

> <big>첫</big>번째 이유의 원인은  
>   -	자바가 동시성 처리를 하는 데 번거로움이 있기 때문이다.  
>   이를 해결하려고 넷플릭스는 클라이언트의 요청을 처리하는 **서비스 계층**에서 동시성을 적극적으로 끌어안았다.  
>    <br/>클라이언트의 요청을 처리할 때  
>   **1. 다수의 비동기 실행 흐름(스레드 등)을 생성**하고  
>   그것의 **2.결과를 취합하여 최종 리턴하는 방식으로 내부 로직을 변경**했다.
> <!-- • --> 
> <br/>
> <big>두</big>번째 이유의 원인은  
> 
> 2013년 당시 자바 8에서 제공하는 CompletableFuture 같은 클래스가 제공되지 않았기 때문이다.
> 
> <br/> 
> 
> > <big>CompletableFuture ?</big>  
> > 
> > 자바8부터 제공됨. 완전한 비동기식 병렬 프로그래밍.  
> > 미래에 처리할 업무[Task]로서 Task결과가 완료되었을 때 값을 리턴하거나,  
> > 다른 Task가 실행되도록 발화[Trigger]시키는 Task.
> >   
> > - 장점 
> > 1.	명시적 쓰레드 선언 없이 쓰레드를 사용할 수 있다.  
> > 2.	함수형 프로그래밍 방식으로 비동기적으로 동시성/병렬 프로그래밍을 가능하게 함으로서 의도를 명확하게 드러내는 함축적인 프로그래밍을 가능하게 한다.  
> > 3.	각 Task마다 순서적 연결을 할 수도 있고, Task의 예외 처리도 가능하다.  
> 
> 그래서 비동기 흐름을 조합할 방법이 거의 없었다.  
> RxJava에서는 이를 해결하려고 비동기 흐름을 조합[Compose]할 수 있는 방법을 제공한다.  
> RxJava에서는 조합하는 실행 단위를 **리액티브 연산자[Operator]** 라고 한다  
> 
> <br/>
> <big>세</big>번째 이유의 원인은,
> 콜백이 콜백을 부르는 <b>콜백 지옥</b>의 상황이 코드의 가독성을 떨어뜨리고 문제 발생 시 디버깅을 어렵게 만들기 때문이다. 
> 비동기 방식으로 동작하는 가장 대표적인 프로그래밍 패턴은 콜백 이다
> 그래서 RxJava는 <b>콜백을 사용하지 않는 방향으로 설계</b>해 이를 해결했다.


리액티브 프로그래밍은 비동기 연산을 필터링, 변환, 조합해 위 세가지 핵심 이유를 해결할 수 있다.

따라서 RxJava는 Observable과 같은 데이터 소스와 map(), filter(), reduce()와 같은 리액티브 연산자를 제공한다.

참고로 RxJava는 2016년 10월에 완전히 새로 작성한 RxJava2.0을 발표했다. 

RxJava2.0은 RxJava1.x를 <b>Reactive-Stream 스펙 기반으로 새롭게 개선한 것</b>이므로 공통점도 많고 차이점도 많다.

Reactive-Stream 스펙은 자바 8에 도입된 Stream API와 Observable 기반의 리액티브 프로그래밍을 포괄하는 표준 스팩으로 자바 9에 도입될 예정이다.

<br/>

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## 3. 데이터 소스 클래스들 

RxJava 1.x버전에서는 데이터 소스가 Observable과 Single 뿐이었지만, 2.x버전으로 올라오면서 Observable이 Observable과 Maybe, Flowable 클래스로 세분화 되었다.

### 1. Observable 클래스 [ 차가운 Observable ]
<!-- 
> Observable은 <b>옵저버 패턴</b>을 구현한다.  
> 옵저버 패턴은 객체의 상태 변화를 관찰하는 관찰자[ 옵저버 ] 목록을 객체에 등록한다.  
> 그리고 상태 변화가 있을 때마다 메서드를 호출하여 객체가 직접 목록의 각 옵서버에게 변화를 알려준다.  
> 라이프 사이클은 존재하지 않으며, 보통 <b>단일 함수를 통해 변화만 알린다</b>.
 -->
> Observable은 세가지의 알림을 구독자[ Subscribe ]에게 전달한다.
> 1. onNext() : Observable이 데이터의 발행을 알림
> 2. onError() : Observable에서 어떤 이유로 에러가 발생했음을 알리고 Observable 종료
> 3. onComplete() : 모든 데이터의 발행이 완료됨을 알림
> 
> <br/>
> Observable을 생성할 때는 직접 인스턴스를 만들지 않고, <b>정적 팩토리 함수 [== 생성 연산자]</b>를 호출한다.  
> 
> - 기본 팩토리 함수 : create(), just(), fromArray(), fromIterable(), fromCallable(), fromFuture(), fromPublisher()  
> - 기타 팩토리 함수 : interval(), intervalRange(), range(), timer(), defer(), repeat()

#### 기본 팩토리 함수
> create(), just() , fromArray(), fromIterable(), fromCallable(), fromFuture(), fromPublisher()

- create()
    > onNext(), onComplete(), onError()같은 알림을 개발자가 직접 호출해줘야 한다.  
    > 구독자에게 데이터를 발행하려면 <b>onNext()</b>함수를 호출해야 하며,  
    > 모든 데이터를 발행한 후에는 반드시 <b>onComplete()</b>함수를 호출해야 한다.  
    > 
    > <br/>
    > 
    > 예제
    > ```java
    > Observable.create((emitter)->{            
    >                 emitter.onNext(100);      
    >                 emitter.onNext(200);      
    >                 emitter.onNext(300);      
    >                 emitter.onComplete();     
    >            })                            
    >         .subscribe(System.out::println);  
    >         /*
    >             System.out::println은 원형이 다음과 같다.
    >             data -> System.out.println(data);
    >             위의 람다 표현식을 
    >             메서드 레퍼런스[ 자바 8부터 사용 ]를 이용해서 줄인 것.
    >         */
    > ```
    > 
    > <br/>
    > 
    > 결과
    > ```
    > rxjava I/System.out: 100
    > rxjava I/System.out: 200
    > rxjava I/System.out: 300
    > ```
    
    > `참고 1`  
    > subscribe() 사용 방법  
    > 
    > 1. 인자를 주지 않은 경우 : .subscribe()  
    > onNext와 onComplete를 무시하고 onError 이벤트가 발생했을 경우 OnErrorNotImplementedException을 던진다.  
    > --> Observable로 작성한 코드를 테스트하거나 디버깅할 때 활용  
    > 
    > 2. 인자를 하나만 준 경우 : .subscribe(next->syso(next))  
    > onNext 이벤트를 처리한다. 역시 onError 이벤트가 발생한 경우 OnErrorNotImplementedException을 던진다.  
    > 
    > 3. 인자를 두개 준 경우 : .subscribe(next->syso(next), err->syso(err))  
    > onNext 와 onError 이벤트를 처리한다.  
    > 
    > 3. 인자를 세개 준 경우 : .subscribe(next->syso(next), err->syso(err), ()->syso("complete"))  
    > onNext , onError , onComplete 이벤트를 모두 처리한다.  

    > `참고 2 `  
    > RxJava의 javaDoc에 따르면 create()는 RxJava에 익숙한 사용자만 활용할 수 있도록 권고한다.
    > 사실 create()를 사용하지않고 다른 팩토리 함수를 활용하면 같은 효과를 낼 수 있기 때문이다. 
    > create()를 꼭 사용해야 한다면 다음과 같은 사항을 확인해야 한다.
    > 1. Observable이 구독 해지 되었을 때 등록된 콜백을 모두 해지해야 한다. 그렇지 않으면 잠재적으로 메모리 누수(memory leak)가 발생한다.
    > 2. 구독자가 구독하는 동안에만 onNext와 onComplete를 호출해야 한다.
    > 3. 배압[ back pressure : Observable이 항목을 발행하는 속도가 구독 속도보다 훨씬 빠른 경우. 이로 인해 RxJava는 다루기 힘든 양의 시스템 리소스를 점유 할 수 있습니다. [참고](https://github.com/ReactiveX/RxJava/wiki/Backpressure) ]을 직접 처리해야 한다.
    
- just()
    > 인자로 넣은 데이터를 차례로 발행하려고 Observable을 생성한다.  
    > 실제 데이터의 발행은 subscribe()를 호출해야 한다.  
    > 한개 이상 열개 이하의 값을 인자로 넣을수 있고, 이때 인자의 데이터 타입은 모두 같아야 한다.
    > 
    > <br/>
    > 
    > 예제
    > ```java
    > Observable
    >         .just("o", "tw", "th", "fo", "fi", "s", "se", "e", "n", "t")
    >         .subscribe(
    >            next -> System.out.println(next),
    >            error -> System.out.println(error.getMessage()),
    >            () -> System.out.println("complete")
    >    );
    > ```
    > 
    > 
    > 결과
    > ```
    > rxjava I/System.out: o
    > rxjava I/System.out: tw
    > rxjava I/System.out: th
    > rxjava I/System.out: fo
    > rxjava I/System.out: fi
    > rxjava I/System.out: s
    > rxjava I/System.out: se
    > rxjava I/System.out: e
    > rxjava I/System.out: n
    > rxjava I/System.out: t
    > rxjava I/System.out: complete
    > ```

- fromArray()
    > 단일 데이터를 다룰 경우엔 create()나 just()를 사용하고,  
    > 단일 데이터가 아닌 경우엔 fromXXX() 계열 함수를 사용한다.  
    > 
    > 그 중에서도 <b>배열</b>에 들어있는 데이터를 다루는 경우에는 fromArray()함수를 활용한다.
    >
    > <br/>
    > 
    > 예제
    > ```java
    > String[] arr = {"from", "array"};
    > Observable
    >         .fromArray(arr)
    >         .subscribe(
    >                 next -> System.out.println(next),
    >                 error -> System.out.println(error.getMessage()),
    >                 () -> System.out.println("complete")
    >         );
    > ```
    >
    > 결과
    > ```java
    > rxjava I/System.out: from
    > rxjava I/System.out: array
    > rxjava I/System.out: complete
    > ```

- fromIterable()
    > Iterable 인터페이스를 구현한 클래스에서 Observable객체를 생성하는 방법.  
    > 
    > Iterable 인터페이스는 Iterater 패턴을 구현한 것으로,  
    > 다음에 어떤 데이터가 있는지와, 그 값을 얻어오는것만 관여할 뿐, 특정 데이터 타입에 의존하지 않는다.  
    > 
    > Iterable 인터페이스를 구현하는 대표적인 클래스는 ArrayList (List 인터페이스), HashSet (Set 인터페이스), LinkedList, Stack 등이 있다.  
    > 
    > <br/>
    > 
    > 예제
    > ```java
     > List<String> arr = new ArrayList<>();
     > arr.add("array");
     > arr.add("list");
     > arr.add("출력!");
     > 
     > Observable
     >         .fromArray(arr)
     >         .subscribe(
     >                 next -> System.out.println(next),
     >                 error -> System.out.println(error.getMessage()),
     >                 () -> System.out.println("complete")
     >         );
    > ```
    >
    > 결과
    > ```java
    > rxjava I/System.out: array
    > rxjava I/System.out: list 
    > rxjava I/System.out: 출력! 
    > rxjava I/System.out: complete
    > ```
    >
    
    > `참고 1 `  
    > Map 객체는 Iterable 인터페이스를 구현하지 않았으므로 from() 계열 함수가 존재하지 않는다.

- fromCallable()
    > 자바 5에서 추가된 동시성 API인 Callable 인터페이스이다.  
    > Callable인터페이스는 비동기 실행 후 결과를 반환하는 call()메서드를 정의한다.  
    > 
    > <br/>
    > 
    > 예제
    > ```java
     > Callable<String> callable = new Callable<String>() {
     >     @Override
     >     public String call() throws Exception {
     >         System.out.println("start!");
     >         Thread.sleep(1000);
     >         return "end ! hi callllll";
     >     }
     > };
     > 
     > Observable
     >         .fromCallable(callable)
     >         .subscribe(
     >                 next -> System.out.println(next),
     >                 error -> System.out.println(error.getMessage()),
     >                 () -> System.out.println("complete")
     >         );
    > ```
    >
    > 결과
    > ```java
    > 07:28:22.669 I/System.out: start!     
    > 07:28:23.671 I/System.out: end ! hi callllll
    > 07:28:23.671 I/System.out: complete   
    > ```
    >

### 2. Single 클래스
> Single 클래스는 <b>Observable의 특수한 형태</b>이다.  
> 
> observable 클래스는 데이터를 무한하게 발행할 수 있지만  
> Single 클래스는 오직 1개의 데이터만 발행하도록 한정한다.  
> 
> 그렇기 때문에 Single은 onNext와 onComplete가 onSuccess로 통합되어,  
> onSuccess()와 onError()함수만 사용할 수 있다.  
> 
> 보통 결과가 유일한 서버 API를 호출할 때 유용하게 사용한다.
> 
> <br/>
> 
> 예제1
> ```java
> Single                                                       
>       .just("Single~~") // 이때 인자를 하나만 넣을 수 있음
>       .subscribe(                                            
>               success -> System.out.println(success),        
>               error -> System.out.println(error.getMessage())
>       );                                                     
> ```
>
> 결과
> ```java
> rxjava I/System.out: Single~~
> ```
> 
> <br/>
> 
> 예제2
> ```java
> Observable
>         .just("Single~~")
>         .single("default val")
>         .subscribe(
>                 success -> System.out.println(success),
>                 error -> System.out.println(error.getMessage())
>         );                                               
> ```
>
> 결과
> ```java
> rxjava I/System.out: Single~~
> ```
>
> <br/>
> 
> 예제3
> ```java
> Observable
>         .just("Single~~", "two")
>         .single("default val")
>         .subscribe(
>                 success -> System.out.println(success),
>                 error -> System.out.println(error.getMessage())
>         );                                               
> ```
>
> 결과
> ```java
> rxjava I/System.out: Sequence contains more than one element!
> ```
> single는 인자를 하나만 받기 때문에 에러가 발생되었다.

### 3. Maybe 클래스
> RxJava2에 처음 도입된 Observable의 또 다른 특수 형태이다.  
> 
> Single 클래스와 마찬가지로, 인자를 최대 하나 가질 수 있지만,  
> 인자를 가지지 않을수도 있다.  
> 
> Maybe 객체를 생성할 수 있는 리액티브 연산자에는 elementAt(), firstElement(), flatMapMaybe(), lastElement(), reduce(), singleElement() 함수 등이 있다.  

### 4. Flowable 클래스
> Observable에서 데이터가 발행되는 속도가 구독자가 처리하는 속도보다 현저하게 빠른 경우 발생하는 배압 이슈에 대응하기 위해서 Obserable클래스로부터 분리된 클래스이다.  
> 
> 이 클래스의 활용은 기본적으로 Observable클래스와 동일하다.  

#### 4.1 Observable과 flowable의 선택 기준

1. Observable을 사용해야 할 경우

    - 최대 1000개 미만의 데이터 흐름.  
    예를 들어 응용프로그램에서 OOME( Out Of Memory Exception )이 발생할 확률이 거의 없는 경우.
    - 마우스 이벤트나 터치 이벤트를 다루는 GUI 프로그래밍.  
    이 경우에는 배압 이슈가 거의 발생하지 않는다.
    
2. Flowable을 사용해야 할 경우

    - 1000개 이상의 데이터를 처리하는 경우  
    이때 메서드 체인에서 데이터 소스에 데이터 개수 제한을 요청해야 한다.
    - 디스크에서 파일을 읽어 들일 경우  
    본질적으로 블로킹 I/O방식을 활용하고 내가 원하는 만큼 가져오는 방식으로 처리해야 하기 때문이다.  
    예를 들면 , 특정 단위로 잘라 몇 행씩 가져오도록 제어할 수 있다.
    - JDBC를 활용해 데이터베이스의 쿼리 결과를 가져오는 경우, 블로킹 방식을 이용하므로 ,  
    ResultSet.next()를 호출하는 방식으로 쿼리의 결과를 가져올 수 있다.
    - 네트워크 I/O를 실행하는 경우, 네트워크나 프로토콜을 통해 서버에서 가져오길 원하는 만큼의 데이터양을 요청할 수 있을 때이다.
    - 다수의 블로킹 방식을 사용하거나 가져오는 방식의 데이터 소스가 미래에는 논 블로킹 방식의  
    리액티브 API나 드라이버를 제공할 수도 있는 경우이다.

디스크에서 파일 읽기, JDBC를 활용한 데이터베이스 쿼리하기, 네트워크 I/O 등은 차가운 Observable에 해당한다.  
보통 차가운 Observable은 결과 데이터를 한꺼번에 모두 가져온다.  
따라서 이 경우에 반드시 Flowable을 활용해야 하는 것은 아니다.

업스트림에서 발행하는 데이터의 속도와 다운 스트림에서 처리하는 속도의 차이가 작다면 Observable을 활용해도 된다.

즉, 데이터 발행과 처리 속도가 차이 나더라도  
먼저 sample(), throttle(), debounce() 같은 **흐름 제어 함수**를 활용하여 해결해보고,  
이 함수로 해결이 어려운 경우에 Flowable 클래스로 전환하면 된다.

<br/>

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## 4. 뜨거운 옵저버블 [Hot Observable]
> Observable에는 뜨거운 Observable과 차가운 Observable이 있다.  
> 
> 차가운 Observable은 Observable을 선언하고 just(), fromIterable()함수를 호출해도  
> 옵저버가 subscribe()함수를 호출하여 구독하지 않으면 데이터를 발행하지 않는다.  
> 다른 말로 게으른 [== lazy] 접근법 이다.  
> 
> 뜨거운 Observable은 구독자가 존재 여부와 관계없이 데이터를 발행하는 Observable이다.  
> 따라서 여러 구독자를 고려할 수 있다.  
> <sub>=> 데이터의 원천은 한곳이지만, 내가 최종적으로 원하는 결과 데이터가 여러 종류일 때, 각각을 구독자라고 생각하면 됨</sub>  
> 단, 구독자로서는 Observable에서 발행하는 데이터를 처음부터 모두 수신할 것으로 보장할 수 없다.  
> 
> 즉, 차가운 Observable은 (구독자가) 구독하면 준비된 데이터를 <b>처음부터 발행</b>한다.  
> 하지만 , 뜨거운 Observable은 <b>구독한 시점</b>부터 Observable에서 발행한 값을 받는다.  
> 
> 지금까지 다루었던 Observable은 모두 차가운 Observable이다.  
> 뜨거운 Observable의 예로는 마우스 이벤트, 키보드 이벤트, 시스템 이벤트, 센서 데이터와 주식가격 등이 있다.  
> 온도 , 습도 센서의 데이터를 처리하는 앱이라면 최근의 온도, 습도 정보만 사용자에게 표시하면 된다.
> 
> 차가운 Observable을 뜨거운 Observable로 변환하는 방법은 Subject 객체를 만들거나, ConnecableObservable 클래스를 활용하는 것이다.

<br/>

### 1. Subject 클래스
> Subject 클래스는 Observable의 속성과 subscribe의 속성을 모두 가지고 있다.  
> Observable처럼 데이터를 발행할 수도 있고 , 구독자처럼 발행된 데이터를 바로 처리할 수도 있다.  
> 
> 다양한 Subject 클래스가 있지만,  
> AsyncSubject, BehaviorSubject, PublishSubject, ReplaySubject에 대해 알아본다.
> 
> <br/>
> 
> > #### 1. AsyncSubject 클래스
> > Observable에서 발행한 마지막 데이터를 얻어올 수 있는 Subject 클래스 이다.
> > 완료되기 전의 <b>마지막 데이터</b>에만 관심이 있으며 이전 데이터는 무시된다.
> > 
> > create()함수로 생성한다.
> > <br/>
> > 
> > 예제
> > ```java
> > AsyncSubject<String> subject = AsyncSubject.create();
> > subject.subscribe(data -> System.out.println("Subscriber #1 =>"+data));
> > subject.onNext("Start");
> > subject.onNext("중간");
> > subject.subscribe(data -> System.out.println("Subscriber #2 =>"+data));
> > subject.onNext("End");
> > subject.onComplete();
> > subject.onNext("hi"); // complete 시점 이후에는 observable이 종료되기때문에    발행이 안됨.
> > subject.subscribe(data -> System.out.println("Subscriber #3 =>"+data));
> > ```
> >
> > 결과
> > ```java
> > 09:16:51.500 I/System.out: Subscriber #1 =>End
> > 09:16:51.501 I/System.out: Subscriber #2 =>End
> > 09:16:51.502 I/System.out: Subscriber #3 =>End
> > ```
> 
> <br/>
> 
> > #### 2. BehaviorSubject 클래스
> > BehaviorSubject는 구독자가 구독을 하면 가장 최근 값 혹은 기본값을 넘겨준다.  
> > 예를 들어, 온도 센서에서 값을 받아온다면 가장 최근의 온도 값을 받아오는 동작을    구현 할 수 있다.  
> > 또한 온도를 처음 얻을 때는 초깃값을 반환하기도 한다.  
> > 
> > createDefault(T defaultItem)함수로 생성한다.  
> > 
> > <br/>
> > 
> > 예제
> > ```java
> > BehaviorSubject<String> subject = BehaviorSubject.createDefault("0");
> > subject.subscribe(data -> System.out.println("1번 구독자 - 현재 날씨 : "+data   +" ℃"));
> > subject.onNext("5");
> > subject.onNext("12");
> > subject.subscribe(data -> System.out.println("2번 구독자 - 현재 날씨 : "+data   +" ℃"));
> > subject.onNext("24");
> > subject.onComplete();
> > subject.onNext("100");
> > subject.subscribe(data -> System.out.println("3번 구독자 - 현재 날씨 : "+data   +" ℃"));
> > ```
> >
> > 결과
> > ```java
> > 09:31:39.650 I/System.out: 1번 구독자 - 현재 날씨 : 0 ℃       // new
> > 09:31:39.650 I/System.out: 1번 구독자 - 현재 날씨 : 5 ℃
> > 09:31:39.650 I/System.out: 1번 구독자 - 현재 날씨 : 12 ℃
> > 09:31:39.651 I/System.out: 2번 구독자 - 현재 날씨 : 12 ℃      // new
> > 09:31:39.651 I/System.out: 1번 구독자 - 현재 날씨 : 24 ℃
> > 09:31:39.651 I/System.out: 2번 구독자 - 현재 날씨 : 24 ℃
> > ```
> >
> > AsyncSubject()는 onComplete 이벤트가 호출된 이후에도 발행이 되었지만,  
> > BehaviorSubject()는 onComplete 이벤트가 호출된 이후에는 발행이 되지 않는다.  
> > 
> > **BehaviorSubject의 구독자는 구독 시점바로 이전에 발행된 데이터와[ 없다면    default값 ] onComplete 이벤트가 호출 될 때까지의 발행된 데이터를 얻게 된다.**  
> 
> <br/>
> 
> > #### 3. PublishSubject 클래스
> > 가장 평범한 Subject 클래스 이다.
> > 
> > 생성시 create() 팩토리함수로 생성되기 때문에,  
> > BehaviorSubject() 처럼 default 값을 받지도 않고,  
> > AsyncSubject() 처럼 마지막 발행된 값만 받지 않는다.
> > 
> > 구독 시점 이후부터 onComplete가 발생할때까지 발행된 데이터를 전달 받는다.
> > 
> > <br/>
> > 
> > 예제
> > ```java
> > PublishSubject<String> subject = PublishSubject.create();
> > subject.subscribe(data -> System.out.println("1번 구독자 On - 런닝맨 진행률 : "   +data+" %"));
> > subject.onNext("0");
> > subject.onNext("50");
> > subject.subscribe(data -> System.out.println("2번 구독자 On - 런닝맨 진행률 : "   +data+" %"));
> > subject.onNext("80");
> > subject.onNext("100");
> > subject.onComplete();
> > subject.onNext("종료");
> > subject.subscribe(data -> System.out.println("3번 구독자 On - 런닝맨 진행률 : "   +data+" %"));
> > ```
> >
> > 결과
> > ```java
> > 09:57:38.866 I/System.out: 1번 구독자 On - 런닝맨 진행률 : 0 %
> > 09:57:38.866 I/System.out: 1번 구독자 On - 런닝맨 진행률 : 50 %
> > 09:57:38.867 I/System.out: 1번 구독자 On - 런닝맨 진행률 : 80 %
> > 09:57:38.867 I/System.out: 2번 구독자 On - 런닝맨 진행률 : 80 %
> > 09:57:38.868 I/System.out: 1번 구독자 On - 런닝맨 진행률 : 100 %
> > 09:57:38.868 I/System.out: 2번 구독자 On - 런닝맨 진행률 : 100 %
> > ```
> 
> <br/>
> 
> > #### 4. ReplaySubject 클래스
> > ReplySubject 클래스는 특이하고 사용할 때 주의해야 하는 클래스이다.  
> > 
> > Subject 클래스의 목적은 뜨거운 Observable을 활용하는 것인데,  
> > 차가운 Observable처럼 동작하기 때문이다.  
> > 
> > Replay클래스는 구독자가 새로 생기면 onComplete 이후에 이루어진 구독이라도,  
> > 항상 데이터의 처음부터 끝까지 발행하는 것을 보장해준다.
> > 
> > <br/>
> > 
> > 예제
> > ```java
> > ReplaySubject<String> subject = ReplaySubject.create();
> > subject.subscribe(data -> System.out.println("1번 구독자 On - 로또 번호 확인: "   +data));
> > subject.onNext("3");
> > subject.onNext("6");
> > subject.onNext("10");
> > subject.onNext("11");
> > subject.subscribe(data -> System.out.println("2번 구독자 On - 로또 번호 확인: "   +data));
> > subject.onNext("24");
> > subject.onNext("43");
> > subject.onComplete();
> > subject.onNext("종료");
> > subject.subscribe(data -> System.out.println("3번 구독자 On - 로또 번호 확인: "   +data));
> > ```
> >
> > 결과
> > ```java
> > 10:57:19.312 I/System.out: 1번 구독자 On - 로또 번호 확인: 3		// 1 ON
> > 10:57:19.313 I/System.out: 1번 구독자 On - 로또 번호 확인: 6
> > 10:57:19.313 I/System.out: 1번 구독자 On - 로또 번호 확인: 10
> > 10:57:19.313 I/System.out: 1번 구독자 On - 로또 번호 확인: 11
> > 10:57:19.314 I/System.out: 2번 구독자 On - 로또 번호 확인: 3		// 2 ON
> > 10:57:19.314 I/System.out: 2번 구독자 On - 로또 번호 확인: 6
> > 10:57:19.314 I/System.out: 2번 구독자 On - 로또 번호 확인: 10
> > 10:57:19.315 I/System.out: 2번 구독자 On - 로또 번호 확인: 11       // 2번이    구독하기 전의 데이터를 한번에 받은다음 1번째 구독자부터 순서대로 발행
> > 10:57:19.315 I/System.out: 1번 구독자 On - 로또 번호 확인: 24
> > 10:57:19.315 I/System.out: 2번 구독자 On - 로또 번호 확인: 24
> > 10:57:19.315 I/System.out: 1번 구독자 On - 로또 번호 확인: 43
> > 10:57:19.315 I/System.out: 2번 구독자 On - 로또 번호 확인: 43
> > 10:57:19.317 I/System.out: 3번 구독자 On - 로또 번호 확인: 3		// 3 ON
> > 10:57:19.317 I/System.out: 3번 구독자 On - 로또 번호 확인: 6
> > 10:57:19.317 I/System.out: 3번 구독자 On - 로또 번호 확인: 10
> > 10:57:19.317 I/System.out: 3번 구독자 On - 로또 번호 확인: 11
> > 10:57:19.317 I/System.out: 3번 구독자 On - 로또 번호 확인: 24
> > 10:57:19.317 I/System.out: 3번 구독자 On - 로또 번호 확인: 43
> > ```

<br/>

### 2. ConnectableObservable 클래스
> ConnectableObservable 클래스 역시 Subject 클래스처럼 차가운 Observable을 뜨거운 Observable로 변환한다.  
> 
> Observable처럼 데이터를 발행할 수도 있고 , 구독자처럼 발행된 데이터를 바로 처리할 수도 있다.  
> 
> Observable을 여러 구독자에게 공유할 수 있으므로 원 데이터 하나를 여러 구독자에게 동시에 전달할때 사용한다.  
> 
> 특이한 점은 <b>connect()함수를 호출하기 전까지는</b> subscribe()함수를 호출해도 아무 동작이 일어나지 않는다는 것이다.  
> 새로 추가된 connect()함수는 호출한 시점부터 subscribe()함수를 호출한 구독자에게 데이터를 발행하기 때문이다.
> 
> ConnectableObservable 객체를 생성하려면 먼저 Observable에 <b><big>publish()</big></b> 함수를 호출해야 한다.  
> 이 함수는 <b>여러 구독자에게 데이터를 발행하기 위해 connect()함수를 호출하기 전까지 데이터 발행을 유예하는 역할</b>을 한다.
> 
> <br/>
> 
> 예제
> ```java
> try {
>     String[] arr = {"첫째", "둘째", "셋째"};
>     Observable<String> balls = Observable.interval(100L, TimeUnit.MILLISECONDS)
>             .map(Long::intValue)
>             .map(i->arr[i])
>             .take(arr.length);    
>     // 100 MilliSeconds 단위로 0부터 데이터 발행
> 
>     ConnectableObservable<String> source = balls.publish();
> 
>     source.subscribe(data -> System.out.println("구독자 #1 =>"+data));
>     System.out.println("발행선언 : 구독자 #1 ");
> 
>     source.subscribe(data -> System.out.println("구독자 #2 =>"+data));
>     System.out.println("발행선언 : 구독자 #2 ");
> 
>     source.connect();
>     System.out.println("connect() 호출");
> 
>     Thread.sleep(250L);
> 
>     source.subscribe(data -> System.out.println("구독자 #3 =>"+data));
>     System.out.println("발행선언 : 구독자 #3 ");
> 
>     Thread.sleep(100L);
> } catch (InterruptedException e) {
>     e.printStackTrace();
> }
> ```
>
> 결과
> ```java
> 11:49:24.183 I/System.out: 발행선언 : 구독자 #1 
> 11:49:24.184 I/System.out: 발행선언 : 구독자 #2 
> 11:49:24.187 I/System.out: connect() 호출
> 11:49:24.287 I/System.out: 구독자 #1 =>첫째
> 11:49:24.287 I/System.out: 구독자 #2 =>첫째
> 11:49:24.388 I/System.out: 구독자 #1 =>둘째
> 11:49:24.388 I/System.out: 구독자 #2 =>둘째
> 11:49:24.440 I/System.out: 발행선언 : 구독자 #3 
> 11:49:24.488 I/System.out: 구독자 #1 =>셋째
> 11:49:24.488 I/System.out: 구독자 #2 =>셋째
> 11:49:24.488 I/System.out: 구독자 #3 =>셋째
> ```

<br/>

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## 연산자에 대해.. 
> [ReactiveX 문서의 연산자 부분](http://reactivex.io/documentation/ko/operators.html)을 보면  
> 다양한 연산자 함수가 존재한다. [ 리액티브 연산자는 함수라고함! ]  
> 이 연산자들은 버전이 높아지면서 계속 늘어난다.  
> 
> 2017년 5월까지 등록된 연산자의 개수는 400여개가 조금 넘었다.  
> 
> 처음 RxJava를 접하면 방대한 양에 , 막막함을 느끼겠지만,  
> 필수 연산자들의 개념을 알면 나머지는 필수 연산자에서 파생된 연산자이므로 이해할수 있기 때문에 모든 연산자를 알 필요는 없다.  
> 
> 그렇기 때문에 모든 함수형 연산의 기본이 되는  
> map(), flatMap(), filter(), reduce() 함수만 공부를 해보도록 하자 !  
> 
> [ 참고 ] 리액티브 연산자 분류  
> <img src="./img/operator.png" alt="operator" width=800>

<br/>

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## 5. Map(), filter(), reduce(), flatMap()함수의 사용법

### 1. map() 함수
> 입력값을 <b>어떤 함수에 넣어서</b> 원하는 값으로 변환하는 함수이다.  
> String을 String으로 변환할 수도 있고, String을 Integer나 다른 객체로 변환할 수도 있다.  
> 
> map() 함수에 함수를 넘겨서 결과를 내려면 java에서 쓰듯이  
> ```java
> public void getSum(String val){
>     System.out.println(val + "<>");
> }
> ```
> 위와 같은 getSum()메서드를 넘길수가 없다. 자바는 인자로 힘수를 넘길 수가 없기 때문이다.  
> 대신에, RxJava에서 지원하는 Function 인터페이스를 사용하면 된다.  
> 
> <br/>
> 
> 예제 1 : Functoin 인터페이스 사용
> ```java
> Function<String, String> getSum = value -> value+" 펑션 써보낳";
> String[] result = {"1", "2", "3", "4"};
> Observable<String> source = Observable.fromArray(result)
>         .map(getSum);
> source.subscribe(System.out::println); 
> ```
> 
> 예제 1의 lambda 표현식
> ```java
> String[] result = {"1", "2", "3", "4"};
> Observable<String> source = Observable.fromArray(result)
>         .map(value -> value+" 펑션 써보낳");
> source.subscribe(System.out::println); 
> ```
> 
> 결과
> ```
> rxjava I/System.out: 1 펑션 써보낳
> rxjava I/System.out: 2 펑션 써보낳
> rxjava I/System.out: 3 펑션 써보낳
> rxjava I/System.out: 4 펑션 써보낳
> ```

### 2. flatMap() 함수
> map()을 발전시킨 함수로, <b>결과가 Observable</b>로 나온다.
> 
> map()이 일대일 함수라면, flatMap()함수는 일대다 혹은 일대일 Observable 함수이다.
> 
> 예제
> ```javascript
> Function<String, Observable<String>> get = value -> Observable.just(value + "test", value + "flat", value + "Map");   
> String[] result = {"1 ", "2 "}; 
> Observable<String> source = Observable.fromArray(result) 
>         .flatMap(get);
> source.subscribe(System.out::println);
> ```
> 
> 결과
> ```
> rxjava I/System.out: 1 flat
> rxjava I/System.out: 1 Map
> rxjava I/System.out: 1 test
> rxjava I/System.out: 2 test
> rxjava I/System.out: 2 flat
> rxjava I/System.out: 2 Map
> ```

### 3. filter() 함수
> Observable에서 원하는 데이터만 걸러내는 역할을 한다.  
> 즉, 필요없는 데이터는 제거하고, 오직 관심 있는 데이터만 filter()함수를 통과하게 된다.  
> 
> filter() 함수에는 boolean값을 리턴하는 함수형 인터페이스인 Predicate를 인자로 넣는다.
> 
> 예제 : Predicate 인터페이스 사용
> ```java
> Predicate<String> filterMonkey = val -> val.endsWith("MONKEY");
> 
> String[] result = {"1 DOG", "2 MONKEY", "3 BEAR", "4 BEER"};
> Observable<String> source = Observable.fromArray(result)
>         .filter(val -> val.endsWith("MONKEY"));
> source.subscribe(System.out::println);
> ```
> 
> 예제 : lambda 사용
> ```java
> String[] result = {"1 DOG", "2 MONKEY", "3 BEAR", "4 BEER"};
> Observable<String> source = Observable.fromArray(result)
>         .filter(val -> val.endsWith("MONKEY"));
> source.subscribe(System.out::println);
> ```
> 
> 결과 
> ```
> rxjava I/System.out: 2 MONKEY
> ```

#### filter() 함수와 비슷한 함수들 

1. first(T default) 함수 : Observable의 첫 번째 값을 필터함. 만약 값 없이 완료되면 기본값 리턴

2. last(T default) 함수 : Observable의 마지막 값을 필터함. 만약 값 없이 완료되면 기본값 리턴

3. take(int N) 함수 : 최초 N개 값을 가져옴 ------ ★★★★★

4. takeLast(int N) 함수 : 마지막 N개 값만 필터함

5. skip(int N) 함수 : 최초 N개 값을 건너뜀

6. skipLast(int N) 함수 : 마지막 N개 값을 건너뜀

### 4. reduce() 함수
> 발행한 데이터를 모두 사용하여 어떤 최종 결과 데이터를 합성할 때 활용한다.  
> 
> 리턴타입이 Observable이 아니고 , Maybe인데  
> reduce()의 인자로 넘긴 람다 표현식에 의해 결과 없이 완료될 수도 있기 때문이다.  
> 
> 인자로 BiFunction<T, T, T> 인터페이스를 받는다.  
> BiFunction<T1, T2, R> 인터페이스에서 T1과 T2는 입력 값의 자료형이고 , R은 처리 결과 데이터 타입 이다.
> 
> 예제 : BiFunction 인터페이스 사용 
> ```java
> BiFunction<String, String, String> biFun = (data1, data2) -> data1+" → "+data2;
> 
> String[] result = {"1 DOG", "2 MONKEY", "3 BEAR", "4 BEER"};
> Maybe<String> source = Observable.fromArray(result)
>         .reduce(biFun);
> source.subscribe(System.out::println);
> ```
> 
> 예제 : lambda 사용 
> ```java
> String[] result = {"1 DOG", "2 MONKEY", "3 BEAR", "4 BEER"};
> Maybe<String> source = Observable.fromArray(result)
>         .reduce((data1, data2) -> data1+" → "+data2);
> source.subscribe(System.out::println);
> ```
> 
> 결과
> ```java
> rxjava I/System.out: 1 DOG → 2 MONKEY → 3 BEAR → 4 BEER
> ```

<br/> 

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## ~~6. 생성 연산자, 결합 연산자, 변환 연산자 등 카테고리별 주요 함수~~
<!--  
### 1. 생성 연산자
> interval() 함수, timer()함수, range()함수, intervalRange()함수, defer()함수, 
> #### interval() 함수 -->

<br/>

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## 7. 스케쥴러의 의미, subscribeOn()과 observeOn() 함수의 차이
> Observable만큼이나 중요하다.  
> 어떤 프로그램의 세부 일정을 주관하는 관리자쯤 ?!  

### 1. 스케줄러란 ? 
> 지금까지 배운 예제의 공통점은 대부분의 동작이 현재 스레드에서 동작한다는 것이다.  
> 그런데 실무에서는 요구사항에 맞게 비동기로 동작할 수 있도록 이를 바꿔야 한다.  
> 이때, 스케줄러를 사용한다.  
> 
> 스레드 확인 예제
> ```java
> final String THREAD_NM = Thread.currentThread().getName();
> 
> Observable.just("hello", "rx")
>         .subscribe(data->System.out.println(THREAD_NM+"||"+data));
> ```
>
> 결과
> ```java
> rxjava I/System.out: main||hello
> rxjava I/System.out: main||rx
> ```
> 
> 스케줄러 요점  
> ```
> 1. 스케줄러는 RxJava 코드를 어느 스레드에서 실행할지 지정할 수 있다.
> 2. subscribeOn() 함수와 observeOn()함수를 모두 지정하면  
>    Observable에서 데이터 흐름이 발생하는 스레드와 처리된 결과를  
>    구독자에게 발행하는 스레드를 분리할 수 있다.  
> 3. subscribeOn()함수만 호출하면 Observable의 모든 흐름이 동일한 스레드에서 실행된다.  
> 4. 스케줄러를 별도로 지정하지 않으면 현재(main) 스레드에서 동작을 실행한다.
> ```

### 2. 스케줄러 종류
> rx의 장점은 특정 스케줄러를 사용하다가 다른 스케줄러로 변경하기 쉽다는 것이다.  
> 마치 map()함수를 한 번 더 호출하는것처럼 새롭게 스케줄러를 추가하거나 기존의 스케줄러를 다른 것으로 교체할 수 있다.  
>  
> #### rxJava에서 제공하는 스케줄러  
> > 뉴 스레드 스케줄러, 계산 스케줄러, IO스케줄러, 트렘펄린 스케줄러, 싱글 스레드 스케줄러  
>  
> #### rxAndroid에서 제공하는 스케줄러  
> > AndroidSchedulers.mainThread() : 안드로이드의 UI스레드에서 동작  
> > HandlerScheduler.from(handler) : 특정 핸들러에 의존하여 동작  
> 
> <br/>
> 
> #### 2.1 뉴 스레드 스케줄러  
> > 이름처럼 새로운 스레드를 생성한다.  
> > 새로운 스레드를 만들어 어떤 동작을 실행하고 싶을 때  
> > Schedulers.newThread()를 인자로 넣어주면 된다.  
> > 그럼 뉴 스레드 스케줄러는 요청을 받을때마다 새로운 스레드를 생성한다.  
> > 
> > rx의 스케줄러는 subscribeOn() 함수와 observeOn()함수에 나눠서 적용할 수 있는데,  
> > 두 함수의 개념을 정확히 알지 못한 상태에서 사용하면 오히려 더 혼란스러울테니,  
> > subscribeOn()에만 스케줄러를 적용했다.  
> > 
> > 예제
> > ```java
> > try {
> >     String[] args ={"1", "2", "3"};
> >     Observable.fromArray(args)
> >             .doOnNext(data -> System.out.println(Thread.currentThread().getName()+"||Origin data : "+ data))
> >             .map(data -> "<< "+data+" >>")
> >             .subscribeOn(Schedulers.newThread())
> >             .subscribe(data->System.out.println(Thread.currentThread().getName()+"||"+data));
> > 
> >     Thread.sleep(500L);
> > 
> >     Observable.fromArray(args)
> >             .doOnNext(data -> System.out.println(Thread.currentThread().getName()+"||Origin data : "+ data))
> >             .map(data -> "## "+data+" ##")
> >             .subscribeOn(Schedulers.newThread())
> >             .subscribe(data->System.out.println(Thread.currentThread().getName()+"||"+data));
> > 
> >     Thread.sleep(500L);
> > } catch (InterruptedException e) {
> >     e.printStackTrace();
> > }
> > ```
> > 
> > 결과
> > ```java
> > 03:03:45.740 rxjava I/System.out: RxNewThreadScheduler-1||Origin data : 1   // Schedulers.newThread()에 의해서 새로운 스레드로 시작
> > 03:03:45.740 rxjava I/System.out: RxNewThreadScheduler-1||<< 1 >>
> > 03:03:45.740 rxjava I/System.out: RxNewThreadScheduler-1||Origin data : 2
> > 03:03:45.740 rxjava I/System.out: RxNewThreadScheduler-1||<< 2 >>
> > 03:03:45.741 rxjava I/System.out: RxNewThreadScheduler-1||Origin data : 3
> > 03:03:45.741 rxjava I/System.out: RxNewThreadScheduler-1||<< 3 >>
> > 03:03:46.241 rxjava I/System.out: RxNewThreadScheduler-2||Origin data : 1   // Schedulers.newThread()에 의해서 새로운 스레드로 시작
> > 03:03:46.241 rxjava I/System.out: RxNewThreadScheduler-2||## 1 ##
> > 03:03:46.241 rxjava I/System.out: RxNewThreadScheduler-2||Origin data : 2
> > 03:03:46.241 rxjava I/System.out: RxNewThreadScheduler-2||## 2 ##
> > 03:03:46.241 rxjava I/System.out: RxNewThreadScheduler-2||Origin data : 3
> > 03:03:46.241 rxjava I/System.out: RxNewThreadScheduler-2||## 3 ##
> > ```
> > 
> > 뉴 스레드 스케줄러는 새로운 그레드를 생성하여 내가 원하는 동작을 처리하는 방법이다.  
> > 하지만 적극적으로 추천하는 방법은 아니다.  
> > rxJava에는 뉴 스레드 스케줄러보다 활용도가 높은  
> > <b><big>계산 스케줄러</big></b>와 <b><big>IO 스케줄러</big></b>와 같은 다른 스케줄러를 제공하기 때문이다.  
> 
> <br/>
> 
> #### 2.2 계산 스케줄러  
> > - rxJava에서 <b>추천하는 스케줄러</b>는 크게 세가지 이다.  
> > 첫 번째는 계산 스케줄러 [ computation ] ,  
> > 두 번째는 IO 스케줄러 ,  
> > 마지막으로 트램펄린 스케줄러 이다.  
> > 
> > 앞서 배운 뉴 스레드 스케줄러나 다른 스케줄러는 특수한 상황에서 사용하는것을 권장한다.  
> > 
> > - 계산 스케줄러는 CPU에 대응하는 계산용 스케줄러 이다.  
> > '계산'작업을 할 때는 대기 시간 없이 빠르게 결과를 도출하는 것이 중요하다.  
> > 계산작업은 입출력[I/O] 작업을 하지 않는 스케줄러라고 생각하면 된다.  
> > 내부적으로 스레드 풀을 생성하며, 스레드 개수는 기본적으로 CPU 개수와 동일하다. 
> > 
> > 
> > 예제
> > ```java
> > String[] args ={"1", "3", "5"};
> > 
> > Observable<String> source = Observable.fromArray(args)
> >         .zipWith(Observable.interval(100L, TimeUnit.MILLISECONDS), (a,b) -> a);
> >
> > // subscribe1
> > source.map(item -> "<<"+item+">>")
> >         .subscribeOn(Schedulers.computation())
> >         .subscribe(data -> System.out.println(Thread.currentThread().getName()+"||"+data));
> >
> > // subscribe2
> > source.map(item -> "##"+item+"##")
> >         .subscribeOn(Schedulers.computation())
> >         .subscribe(data -> System.out.println(Thread.currentThread().getName()+"||"+data));
> > ```
> >
> > 결과
> > ```java
> > 03:24:42.167 rxjava I/System.out: RxComputationThreadPool-2||##1##
> > 03:24:42.168 rxjava I/System.out: RxComputationThreadPool-1||<<1>>
> > 03:24:42.254 rxjava I/System.out: RxComputationThreadPool-2||##3##
> > 03:24:42.255 rxjava I/System.out: RxComputationThreadPool-1||<<3>>
> > 03:24:42.355 rxjava I/System.out: RxComputationThreadPool-2||##5##
> > 03:24:42.355 rxjava I/System.out: RxComputationThreadPool-1||<<5>>
> > ```
> > 
> > 테스트를 여러번 하다보면 아래와 같이 하나의 스레드에서 실행된 결과를 확인할 수도 있는데,  
> > 이것은 첫번째 구독과 두 번째 구독이 거의 동시에 이루어지기 때문에 rxJava 내부에서 동일한 스레드에 작업을 할당했기 때문이다.  
> > 
> > 결과
> > ```java
> > 03:25:17.711 rxjava I/System.out: RxComputationThreadPool-2||<<1>>
> > 03:25:17.711 rxjava I/System.out: RxComputationThreadPool-2||##1##
> > 03:25:17.814 rxjava I/System.out: RxComputationThreadPool-2||<<3>>
> > 03:25:17.815 rxjava I/System.out: RxComputationThreadPool-2||##3##
> > 03:25:17.910 rxjava I/System.out: RxComputationThreadPool-2||<<5>>
> > 03:25:17.913 rxjava I/System.out: RxComputationThreadPool-2||##5##
> > ```
> > 
> <br/>
> 
> #### 3.3 IO 스케줄러  
> > 계산 스케줄러와는 다르게 네트워크상의 요청을 처리하거나,  
> > 각종 입출력 작업을 실행하기 위한 스케줄러 이다.  
> > 
> > 계산 스케줄러와 다른 점은 기본으로 생성되는 스레드 개수가 다르다는 것이다.  
> > - 계산 스케줄러의 스레드 개수 = CPU 개수  
> > - IO 스케줄러의 스레드 개수 = 필요할 때마다 스레드 계속 생성  
> > 
> > 입출력 작업은 비동기로 실행되지만, 결과를 얻기까지 대기 시간이 길다.  
> > 
> > ||계산 스케줄러 | IO스케줄러 |  
> > |--|--|--|  
> > |용도|일반적인 계산 작업 | 네트워크상의 요청, DB쿼리, 파일 입출력|  
> > |최대 스레드 생성개수|최대 CPU개수 만큼 생성됨|필요시 계속 생성|
> 
> <br/>
> 
> #### 4.4 트렘펄린 스케줄러  
> > 새로운 스레드를 생성하지 않고,  
> > <b>현재 스레드에 무한한 크기의 대기 행렬을 생성</b>하는 스케줄러 이다.  
> > 
> > 예제
> > ```java
> > String[] args = {"1","3","5"};
> > 
> > Observable<String> source = Observable.fromArray(args);
> > 
> > // 구독 1
> > source.subscribeOn(Schedulers.trampoline())
> >         .map(data -> "<<"+data+">>")
> >         .subscribe(this::syso);
> > 
> > // 구독 2
> > source.subscribeOn(Schedulers.trampoline())
> >         .map(data -> "##"+data+"##")
> >         .subscribe(this::syso);
> > 
> > 
> > public void syso(String result){
> >     System.out.println(Thread.currentThread().getName()+"||"+result);
> > }
> > ```
> > 
> > 결과
> > ```java
> > 12-13 04:02:44.439 9279-9279/com.zzzhyun.rxjava I/System.out: main||<<1>>
> > 12-13 04:02:44.439 9279-9279/com.zzzhyun.rxjava I/System.out: main||<<3>>
> > 12-13 04:02:44.440 9279-9279/com.zzzhyun.rxjava I/System.out: main||<<5>>
> > 12-13 04:02:44.442 9279-9279/com.zzzhyun.rxjava I/System.out: main||##1##
> > 12-13 04:02:44.442 9279-9279/com.zzzhyun.rxjava I/System.out: main||##3##
> > 12-13 04:02:44.442 9279-9279/com.zzzhyun.rxjava I/System.out: main||##5##
> > ```
> 
> <br/>
> 
> #### 5.5 싱글 스레드 스케줄러  
> > rxJava 내부에서 <b>단일 스레드</b>를 별도로 생성하여 구독 작업을 처리한다.  
> > 단, 생성된 스레드는 여러 번 구독 요청이 와도 <b>공통으로 사용</b>한다.  
> > 
> > 리액티브 프로그래밍이 비동기 프로그래밍을 지향하기 때문에 싱글 스레드 스케줄러를 활용할 확률은 낮다.  
> > 
> > 예제
> > ```java
> > Observable<Integer> numbers = Observable.range(100, 5);
> > Observable<String> strs = Observable.just("one" , "two", "three");
> > numbers.subscribeOn(Schedulers.single())
> >         .subscribe(this::syso);
> > strs.subscribeOn(Schedulers.single())
> >         .subscribe(this::syso);
> > ```
> > 
> > 결과
> > ```java
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||100
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||101
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||102
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||103
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||104
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||one
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||two
> > 04:12:14.776 rxjava I/System.out: RxSingleScheduler-1||three
> > ```
> <br/>


### 3. subscribeOn()
> 구독자가 Observable에 subscribe() 함수를 호출하여 <b>구독할 때 실행되는 스레드</b> 지정  
> subscribeOn()함수는 처음 지정한 스레드를 고정시키므로 , 다시 subscribeOn()을 호출해도 무시한다.  

### 4. observeOn()
> Observable에서 생성한 데이터 흐름이 여기저기 함수를 거치며 처리될 때,  
> 동작이 어느 스레드에서 일어나는지 지정할 수 있다.  
> == 처리된 결과를 구독자에게 전달하는 스레드를 지정.  
> 
> observeOn()함수는 여러번 호출할 수 있으며, 호출시마다 지정한 스레드로 변경된다.


<br/>

-------
[↑ Top](#rxjava--reactivex를-java에서-사용할수-있게-해주는-라이브러리)

## ~~8. 그 밖의 디버깅, 흐름 제어 함수를 익힘~~
