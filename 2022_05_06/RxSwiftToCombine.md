# RxSwift to Combine 

목표 : [RxSwift to Combine (박보영님)](https://www.youtube.com/watch?v=Y273NDkYBg4) 발표 영상을 보고 RxSwift와 Combine을 비교, 대략적인 감을 익혀본다.

## 목차
1. 개념 비교하기
2. 예제로 확인하기
3. 정리

## 개념을 비교하기 앞서 Apple은 왜 Combine을 만들었나? 🤔
코코아 SDK 내에는 수많은 비동기 인터페이스들이 있습니다.  
이들은 클로저나 Completion Block들을 가지고 받는 API이며, 각각 다른 목적, 기능을 가지고 있습니다.  
`특정 클로저에서 어떤 값을 다른 블록으로 넘기고 싶거나 그런 블록들을 묶어서 최종적인 값을 도출하고 싶을 때는 사용성이 좋지 않다.`  
이러한 사용성 개선을 위해서 개념들을 새로 set하기 보단 **공통적인 개념이 있지 않을까?** 해서 나타난 것이 **Combine**이다.

## [Combine이란 ?](https://developer.apple.com/documentation/combine)
시간이 지남에 따라 발생하는 값 처리를 위해 선언된 API.
> The Combine framework provides a declarative Swift API for processing values over time. 
  
## 개념 비교하기 (Combine ↔️ RxSwift)
 ### 1. 핵심 요소
- Publishers ↔️ Observable
- Subscribers ↔️ Observer
- Operators ↔️ Operators
### 2. 추가 요소
- Subject ↔️ Subject
- Cancellable ↔️ Disposable
- subscribe(on:) ↔️ subscribeOn(_:)
---
- ### Publisher ↔️ Observable
|AnyPublisher|Observable|
|:---:|:---:|
|Value Type|Reference Type|
|Output(Data Type)|Element(Data Type)|
|Failure(Error Type)| ❌ |

```swift 
protocol Publisher {...}
struct AnyPublisher: Publisher {...}  //Value Type

class Observable: ObservableType {..}  //Reference Type
```
`Publisher`는 `protocol`이고, `AnyPublisher`는 `Publisher를 받는 struct` 이므로 **Observable과 매칭되는건 AnyPublisher 라고 볼 수 있다.**

```swift
AnyPublisher<String, Error>     
Observable<Result<String, Error>>
```
`Publiser`는 **Error 타입을 미리 정의**한다.  
이에 반해 `Observable`은 **별도의 Error 타입을 받지 않음**, ➡️ `element`자리에 Result를 주입 한다면 Publisher와 유사한 형태를 가져갈 수 있다.

```swift
AnyPublisher<String, Never>     
Observable<String>
```
`Publisher`는 에러가 발생할 수 없는경우 `Failure`자리에 `Never`라고 타입을 사용할 수 있다.  
`Observable`은 `element`만 표시하면 되는데, 이것이 **Error를 발생시키지 않는다는 의미는 아니다.**

‼️ **즉, Combine은 스트림 생성을 Publisher로 하고 RxSwift는 Observable로 한다.  
다만, Combine의 경우에는 Error타입을 별도로 표기해주어야 한다.** ‼️

---
- ### Operators의 차이점
> [RxSwift에만 있는 Operators 👀](https://youtu.be/Y273NDkYBg4?t=552)  
[Combine에만 있는 Operators 👀](https://youtu.be/Y273NDkYBg4?t=553)  

위 링크는 참고만 하도록 하자.
- try + Operator  
`Combine`에는 `try + Operator` 형태의 `Operator`들이 있고, 모든 Operator 앞에 try를 붙인다고 생성되진 않는다.  
`try를 붙인 Operator`는 **에러를 보다 쉽게 핸들링하게 해주는 Operator 이다.** 
```swift
func map<T>(_ transform: (Output) -> T)
                                  -> Just<T>
func tryMap<T>(_ transform: (Output) throws -> T)
                                  -> Result<T, Error>.Pulbisher
```
`map`의 경우에는 반드시 upstream 에서 받은 **Data Type만 return** 해주는데, `tryMap`의 경우에는 **Result 타입을 return** 해주고 있다.  
즉, `tryMap`은 Data Type뿐만 아니라 map 내부에서 발생된 Error를 별도로 핸들링 할 수 있다.

