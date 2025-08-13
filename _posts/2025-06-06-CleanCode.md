---
layout: post
title: 클린코드에 대해
date: 2025-06-06 17:17:38 +0900
categories: [DevNotes]
tags: [설계철학]
description: 클린코드를 읽고...
toc: true
comments: true
math: true
mermaid: true
---

## CleanCode

### Overview

엉클 밥의 CleanCode를 일부 읽고 기억에 남은 내용을 정리했다.

나쁜 코드와 좋은 코드를 구별하고 더 나아가 좋은 코드로 개선할 방법을 떠올릴 수 있는 코드 감각이 필요하다.

코드가 다시 읽힐 때를 가정하고 코드에 의도를 담아낼 수 있도록 하자.
예측 가능하고 한 문서에 사용된 양식이 다른 소스 파일에서도 유지될 거란 믿음을 깨지 말자.

함수에 대해선 전체적으로 FP의 순수함수와 SRP가 많이 떠올랐다.

### 이름 짓기

함수나 변수의 이름을 지을 때 중요한 것은 **명확한 의미를 담고**있어야 한다는 것이다.

이름 간의 일정한 규칙과 맥락을 가져야 하고, 검색이나 발음이 쉽다면 더 좋다.
더불어 이미 널리 사용되는 이름을 혼자 다르게 사용하거나 잘못된 추측을 하게 만드는 축약이나 개성있는 이름도 피해야 한다.
다른 누군가가 읽었을 때 충분히 **예측 가능하고 의미를 알 수 있어야** 하기 때문이다.
여기서 말하는 누군가에는 미래의 나도 포함된다.

메서드의 이름을 지을 때는 최대한 서술적으로 짓기를 추천한다.
인수는 없는 게 가장 좋고 그 뒤로 1개, 2개이다.
3개는 주의하고 4개는 안 쓰는 것이 좋다.

### 주석보다 코드

주석보다 코드로 의도를 표현할 수 있도록 하자.
다양한 상황에서 주석보단 코드로 의도를 표현할 수 있다.

다음은 몇 가지 예시이다.

```swift
// player가 이벤트 대상인지 체크할 때
if (player.level > 30 && player.isPromotioned) { ... }
vs
if player.isEligibleForEvent() { ... }
```

```swift
// module이 우리 시스템에 의존하는지 파악할 때
if (module.getDependencyList().contain(subSystem.getSystem())) { ... }
vs
let moduleDependecies = module.getDependencyList()
let ourSystem = subSystem.getSystem()

if moduleDependecies.contain(ourSystem)
```

---

주석보다 코드로 표현하고자 하는 이유는 코드의 변화를 주석이 완벽히 따라가지 못 하는 경우가 많기 때문이다.
그렇게 되면 오히려 부적절한 정보를 주는 주석으로 남을 가능성이 생기며, 이는 결국 **주석을 무시하게 되는 습관을 낳게 될 수 있다.**
또는 과한 주석이 주의를 분산시켜 코드를 파악하는 흐름을 되려 방해하거나 무시하게 되는 경우도 경험했다.

주석이 크게 긍정적으로 작용할 때는 의도를 알리거나 결과에 대한 경고를 알리는 경우다.

간혹 아래 코드처럼 닫는 괄호에 어떤 스코프에 해당하는지 설명을 달고 싶어진다면 크기를 줄일 방법을 먼저 고민해보자.

```swift
struct SomeView: View {
	var body: some View {
		List {
			ForEach(array) { item in
				if isPresented {
					do {
						 let foo = try bar()
						 VStack {
							 OtherView { ... }
					} catch {
						VStack {
							Text("Error occurred")
							Button("Retry") {
								retryAction()
							}
						}
					}
				} else {
					HStack {
						if shouldKnow { 
							VStack {
								Text("Alternative View")
								Spacer()
								SomeOtherView()
							}
						} else {
							Text("Fallback")
						}
					}
				}
			}
		}
	}
}
```

### 작고 작은 함수

앞선 예시에서 확인했듯 들여쓰기가 많아질수록 점점 코드를 파악하기 힘들어진다.
함수는 최대한 작게 만들고 가능하다면 들여쓰기가 2단을 넘어가지 않도록 해보자.

특히, **단 한가지의 일만 하도록** 주의해야 한다.

이를 위해 지정된 함수 이름 아래로 추상화 수준이 하나인 단계만 수행하는지 체크해야 한다.
다른 표현으로 나타낼 수 있는 변경이 아니라 의미 있는 다른 이름으로 분리해낼 수 있다면 여러 일을 하고 있는 것이라고 볼 수 있다.
또는 함수 내에서 자연스레 섹션이 나뉘어도 여러 책임을 지는 것이라 볼 수 있겠다.

더불어 함수가 한 가지 작업만 하려면 함수 내의 모든 추상화 수준이 동일해야 한다.
여기서 동일한 추상화 수준이란 **근본 개념과 세부 구현 사항이 혼재되어있지 않음**을 의미한다.
지켜지지 않는다면 읽는 이의 입장에서 어떤 책임을 지는지 혼란스러워질 수 있다.

```swift
func fetchUserData() -> User {
	let url = URL(string: "https://api.example.com/user")! // 저수준
	var request = URLRequest(url: url) // 저수준
	request.httpMethod = "Get" // 저수준
	// 위 코드는 네트워크 요청, 프로토콜의 기본 요소 등을 직접 구성하고있오 저수준 추상화다.
	
	let data = try! URLSession.shared.data(for: request).0 // 저수준
	let json = try! JSONSerialization.jsonObject(with: data, options: []) as! [String: Any] // 저수준
	// 위 코드는 네트워크 요청, 응답 데이터 파싱, JSON 직렬화 등 구체적인 과정을 직접 다루고 있기에 저수준 추상화라 볼 수 있다.
	
	return User(id: json["id"] as! Int, name: json["name"] as! String) // 고수준
	// 반환되는 부분은 파싱된 데이터를 기반으로 비즈니스 객체를 생성한다. 이러한 도메인 모델 생성 및 사용은 비즈니스 로직 수준의 코드로 보다 고수준의 코드다.
}
```

위 코드를 다음과 같이 분리할 수 있다.

```swift
func fetchUserData() -> User { // 고수준 메서드
	let data = fetchUserDataFromAPI() 
	let json = decodeUserJson(data) 
	return makeUser(json)
}

private func fetchUserDataFromAPI() -> Data { // 저수준 메서드
	let url = URL(string: "https://api.example.com/user")! // 저수준
	var request = URLRequest(url: url) // 저수준
	request.httpMethod = "Get" // 저수준
	
	return try! URLSession.shared.data(for: request).0 // 저수준
}

private func decodeUserJson(_ data: Data) -> [String: Any] { // 저수준 메서드
	return try! JSONSerialization.jsonObject(with: data, options: []) as! [String: Any] // 저수준
}

private func makeUser(_ json: [String: Any]) -> Uesr { // 고수준 메서드
	return User(id: json["id"] as! Int, name: json["name"] as! String) // 고수준
}
```

중간 수준을 하나 더 추가하여 나눈다면 다음과 같다.
- 구체적인 세부 구현을 가지는 저수준
- 저수준의 함수를 조합하여 보다 의미있는 단위를 만드는 중간수준
- 특정 도메인의 개념을 처리하는 추상적인 흐름을 가지고 있는 고수준

좀 더 항목을 세분화할 수도 있다.
- 하나의 개념적 단위를 형성하는가?
	- 저수준: 개별적인 시스템 동작을 다룸
	- 중간 수준: 특정 기능을 담당하는 역할
	- 고수준: 위 둘을 조합하여 더 큰 개념을 의미
- 동사 기준으로 추상화를 나누기 (무엇을 한다 vs 어떻게 한다)
	- 저수준: `performHTTPRequest()` → HTTP 요청을 실행한다.
	- 중간 수준: `fetchUserDataFromAPI()` → API에서 데이터를 가져온다.
	- 고수준: `fetchUserData()` → 사용자 데이터를 가져온다(추상적 개념)

---

코드 작성 시에는 위에서 아래로 내려갈 수록 추상화 수준이 낮아지는 게 자연스럽다.

```pseudo
배를 채운다.
밥을 먹는다.
밥을 준비한다.
재료를 준비한다, 없으면 주문한다.
```

### 함수에 대한 규칙

함수를 사용할 때도 예측이 가능해야 한다.
이에 따라 출력 인수(`inout`이나 `&`을 통해 참조하여 직접 수정이 가능한 인수)의 사용은 결과를 예측하기 힘들게 하므로 사용을 최대한 지양하자.
나아가 사이드 이펙트 자체가 없는 것이 좋다.

물론 모든 사이드 이펙트를 없애는 것은 불가능하고 모든 사이드 이펙트가 나쁜 것은 아니며,
예측 불가능하고 불필요한 사이드 이펙트인지가 중요하다.

---

플래그를 인수로 받을 것이라면 차라리 두 개의 함수로 분리하자.
flag에 따라 내부에서 두 가지 이상의 일을 할 테니 말이다.

단항 함수라면 함수 이름과 인수가 동사와 명사로 쌍을 이루면 좋다.

이항 함수라면 두 인수가 하나의 값을 가리키거나 자연적인 순서가 명확해야 좋다.
예를 들어 좌표를 나타내는 Point에는 x와 y의 순서가 자연스럽다.
자연적인 순서가 없다면 어떤 인수가 먼저 들어가야 할지 헷갈리기 때문에 이름에 키워드를 넣어주어도 좋다.

```swift
func write(name) { ... } // 동사 - 명사 쌍
func assertEqual(expected, actual) { ... }
→ func assertExpectedEqualActual(expected, actual) { ... }
```

나는 Swift에서 주로 argument label을 활용하여 다음과 같이 작성했다.

```swift
func getData(form user: User) -> Data
func fetchEntity(by id: String) -> Entity

let data: Data = getData(from: user)
let entity: Entity = fetchEntity(by: id)
```

### 코드 컨벤션과 팀 규칙

- **개인의 선호보다 팀 규칙을 항상 우선시 하기**
- 적절한 행 길이와 빈 행 사용에 주의하기
- 종속 함수는 호출하는 함수 뒤에 두기
- 들여쓰기를 무시하지 말기
- 변수는 사용하는 곳과 가장 가까이, 인스턴스 변수는 상단에 두기
