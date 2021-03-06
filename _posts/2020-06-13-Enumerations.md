---
layout: post
comments: true
title:  "Swift 5.2: Enumerations (열거체)"
date:   2020-06-13 10:00:00 +0900
categories: Swift Language Grammar Error Handling
---

> Apple 에서 공개한 [The Swift Programming Language (Swift 5.3)](https://docs.swift.org/swift-book/) 책의 [Enumerations](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html) 부분[^Enumerations]을 번역하고, 설명이 필요한 부분은 주석을 달아서 정리한 글입니다.
>
> 현재 번역이 진행 중인데, 2020-06-22 에 Swift 5.3 이 발표되어, 이미 번역된 부분과 남은 부분 모두 Swift 5.3 을 기준으로 옮기도록 합니다. 완료된 목록은 [Swift 5.3: Swift Programming Language (스위프트 프로그래밍 언어)]({% post_url 2017-02-28-The-Swift-Programming-Language %}) 에서 확인할 수 있으며, 일부는 Swift 5.2 기준일 수 있습니다.

## Enumerations (열거체)

_열거체 (enumerations)_ 는 관계있는 값 그룹에 '공통 타입' 을 정의하여 이 값들을 코드에서 '타입-안전한 (type-safe)'[^type-safe] 방법으로 사용할 수 있게 해 줍니다.

C 언어에 익숙하다면, C 언어의 열거체는 관계된 이름들에 일종의 정수 값을 할당한다는 것을 알고 있을 것입니다. 스위프트의 열거체는 훨씬 더 유연해서, 열거체의 각 'case 값' 에 값을 제공할 필요도 없습니다. 각 열거체 'case 값' 에 값을 제공할 경우 (이를 _원시 값 (raw value)_ 이라 합니다), 이 값은 '문자열 (string)', '문자 (character)', 또는 '정수 (integer)' 나 '부동-소수점 (float-point)' 타입 어떤 것이든 될 수 있습니다.

또 다른 방법으로, 열거체의 'case 값' 에는 각 'case 값' 에 따라 달리 저장되는 '결합된 값 (associated values)' 이라는 것을 _어떤 (any)_ 타입에 대해서든 지정할 수 있어서, 다른 언어에 있는 '공용체 (unions)' 또는 '변형체 (variants)' 와 흡사한 것을 만들 수 있습니다. 관계된 'case 값' 들의 공통 집합을 하나의 열거체로 정의하면서, 제각각 이에 결합된 적당한 타입의 서로 다른 값 집합을 가지도록 정의할 수 있습니다.

스위프트의 열거체는 그 자체로 '일급 타입 (first-class types)'[^first-class] 입니다. 전통적으로 클래스에서만 지원하던 많은 특징들을 채택했는데, 열거체의 현재 값에 대한 추가적인 정보를 제공하는 '계산 속성 (computed properties)', 열거체가 표현할 값과 관계된 기능을 제공하는 '인스턴스 메소드 (instance methods)' 등이 이에 해당합니다. 열거체에는 '초기자' 를 정의해서 '초기 case 값 (initial case value)' 도 제공할 수 있습니다; 기능을 확대해서 원래 구현 이상으로 확장할 수도 있습니다; 프로토콜을 준수해서 표준 기능을 제공할 수도 있습니다.

이러한 '기능들 (capabilities)' 에 대한 더 자세한 내용은, [Properties (속성)]({% post_url 2020-05-30-Properties %}), [Methods (메소드)]({% post_url 2020-05-03-Methods %}), [Initialization (초기화)]({% post_url 2016-01-23-Initialization %}), [Extensions (확장)]({% post_url 2016-01-19-Extensions %}), 그리고 [Protocols (규약)]({% post_url 2016-03-03-Protocols %}) 을 참고하기 바랍니다.

### Enumeration Syntax (열거체 구문 표현)

열거체를 도입하려면 `enum` 키워드를 사용하며 중괄호 쌍 안에 전체 정의를 작성하면 됩니다:

```swift
enum SomeEnumeration {
  // 여기에서 열거체를 정의합니다.
}
```

다음은 '나침반 (compass)' 의 네 가지 주요 지점을 나타내는 예제입니다:

```swift
enum CompassPoint {
  case north
  case south
  case east
  case west
}
```

열거체에서 정의한 값들 (가령 `north`, `south`, `east`, 그리고 `west`) 은 _열거체 case 값 (enumeration cases)_ 입니다. `case` 키워드를 사용하여 새로운 '열거체 case 값' 을 도입할 수 있습니다.

> 스위프트의 '열거체 case 값' 은 기본적으로 정수 값으로 설정되지 않으며, 이는 C 언어와 오브젝티브-C 언어와는 다른 점입니다. 위의 `CompassPoint` 예제에 있는, `north`, `south`, `east`, 그리고 `west` 는 암시적으로 `0`, `1`, `2`, 및 `3` 이 되지 않습니다. 그 대신, 서로 다른 '열거체 case 값' 그 자체가 '값 (values)' 이며, 명시적으로 정의한 `CompassPoint` 가 그 타입입니다.

여러 'case 값' 을, 쉼표로 구분하여, 한 줄로 나타낼 수 있습니다:

```swift
enum Planet {
case mercury, venus, earth, mars, jupiter, saturn uranus, neptune
}
```

각각의 열거체 정의는 새로운 타입을 정의합니다. 스위프트의 다른 타입들과 마찬가지로, 이 이름 (가령 `CompassPoint` 와 `Planet` 등) 은 대문자로 시작합니다. 열거체의 타입은, 자명하게 이해되도록, 복수형 이름이 아니라 단수형을 부여하도록 합니다:

```swift
var directionToHead = CompassPoint.west
```

`directionToHead` 의 타입은 `CompassPoint` 중 한 가지 값으로 초기화될 때 추론됩니다. `directionToHead` 가 한번 `CompassPoint` 로 선언되고 나면, '단축 점 구문 표현 (shorter dot syntax)' 을 사용하여 다른 `CompassPoint` 값을 설정할 수 있습니다:

```swift
directionToHead = .east
```

`directionToHead` 의 타입을 이미 알고 있으므로, 값을 설정할 때 타입을 뺄 수 있습니다. 이는 명시적으로 타입을 지정한 열거체의 값과 작업할 때 아주 가독성 높은 코드를 만들도록 해 줍니다.

### Matching Enumeration Values with a Switch Statement ('switch' 문으로 열거체 값 맞춰보기)

개별 열거체 값은 `switch` 문으로 맞춰볼 수 있습니다:

```swift
directionToHead = .south
switch directionToHead {
case .north:
  print("Lots of planets have a north")
case .south:
  print("Watch out for penguins")
case .east:
  print("Where the sun rises")
case .west:
  print("Where the skies are blue")
}
// "Watch out for penguins" 를 출력합니다.
```

이 코드는 다음 처럼 이해할 수 있습니다.

“`directionToHead` 의 값을 검토합니다. `.north` 와 같은 경우에는, `"Lots of planets have a north"` 를 출력합니다. `.south` 와 같은 경우에는, `"Watch out for penguins"` 을 출력합니다.

...이렇게 계속됩니다.

[Control Flow (제어 흐름)]({% post_url 2020-06-10-Control-Flow %}) 에서 설명한 것 처럼, 열거체의 'case 값' 을 검토할 때 `switch` 문은 반드시 '빠짐없이 철저해야 (exhaustive)' 합니다. 만약 `.west` 에 대한 `case` 를 생략하면, 이 코드는 컴파일되지 않는데, `CompassPoint` 'case 값' 들에 해당하는 전체 목록을 다 검토하지 않기 때문입니다. 빠짐없이 철처할 것을 요구하는 것은 열거체의 'case 값' 을 우연히 생략하지 않도록 보장합니다.

모든 열거채의 'case 값' 에 대해 `case` 를 제공하는 것이 적절하지 않을 때는, `default` 'case 절' 을 제공해서 명시적으로 알리지 않은 'case 값' 이라면 어떤 것도 다루도록 할 수 있습니다:

```swift
let somePlanet = Planet.earth
switch somePlanet {
case .earth:
  print("Mostly harmless")
default:
  print("Not a safe place for humans")
}
// "Mostly harmless" 를 출력합니다.
```

### Iterating over Enumeration Cases (열거체 case 값에 대해 동작 반복 적용하기)

어떤 열거체에서는, 열거체의 모든 'case 값' 들에 대한 '집합체 (collection)' 을 가지는 것이 유용할 수 있습니다. 이렇게 하려면 열거체의 이름 뒤에 `: CaseIterable` 을 붙여주도록 합니다. 스위프트는 해당 열거체 타입의 모든 'case 값' 들을 위한 '집합체' 를 `allCases` 라는 속성으로 드러냅니다. 예를 들면 다음과 같습니다:

```swift
enum Beverage: CaseIterable {
case coffee, tea, juice
}
let numberOfChoices = Beverage.allCases.count
print("\(numberOfChoices) beverages available")
// "3 beverages available" 를 출력합니다.
```

위 예제에서는, `Beverage.allCases` 를 써서 `Beverage` 열거체의 모든 'case 값' 을 담고 있는 '집합체 (collection)' 에 접근합니다. `allCase` 는 다른 모든 '집합체' 들 처럼 사용할 수 있는데-이 집합체의 원소는 열거체 타입의 인스턴스라서, 이 경우에는 `Beverage` 값이 됩니다. 위 예제는 'case 값' 이 얼마나 많이 있는 지를 계산하며, 아래 예제는 `for` 반복문을 사용하여 모든 'case 값' 들에 동작을 반복 적용합니다.

```swift
for beverage in Beverage.allCases {
  print(beverage)
}
// coffee
// tea
// juice
```

위 예제에서 사용한 구문 표현은 열거체가 [CaseIterable](https://developer.apple.com/documentation/swift/caseiterable) 프로토콜을 준수하도록 표시한 것입니다. 프로토콜에 대한 정보는, [Protocols (규약)]({% post_url 2016-03-03-Protocols %}) 을 참고하기 바랍니다.

### Associated Values (결합된 값)

이전 부분에 있는 예제는 열거체의 'case 값' 이 어떻게 그 자체로 정의된 (그리고 타입을 가진) 값인 지를 보여줍니다. 상수나 변수를 `Planet.earth` 라고 설정할 수 있고, 나중에 검사할 수도 있습니다. 하지만, 이러한 'case 값' 과 나란히 다른 타입의 값을 저장할 수 있다면 유용할 때가 있습니다. 이 추가적인 정보를 _결합된 값 (associated value)_ 이라고 하며, 이는 그 'case 값' 을 코드에서 값으로 사용하는 매 순간마다 달라집니다.

스위프트 열거체에는 어떤 타입의 '결합된 값' 도 저장하도록 정의할 수 있으며, 필요하다면 이 값의 타입이 열거체의 각 'case 값' 마다 달라져도 됩니다. 이와 같은 열거체를 다른 프로그래밍 언어에서는 _discriminated unions (차별화된 공용체)_, _tagged unions (꼬리표 달린 공용체)_, 또는 _variants (변형체)_ 라고 합니다.[^variants]

예를 들어, 서로 다른 두 개의 바코드 타입을 쓰는 제품을 추적하는 '재고 물품 추적 시스템' 을 가정해 봅시다. 일부 제품은 UPC 양식의 1-차원 바코드를 써서, `0` 에서 `9` 까지의 숫자로 된, 이름표를 사용합니다. 각각의 바코드에는 한 자리 수의 시스템에 이어서, 다섯 자리의 '제조업체 코드' 와 다섯 자리의 '제품 코드' 가 있습니다. 맨 뒤에는 한 자리의 검사 코드도 있어서 이 코드를 정확하게 스캔했는 지를 검증합니다:

![1-d barcode](/assets/Swift/Swift-Programming-Launguage/Enumerations-1d-barcode.png)

다른 제품은 QR 코드 양식의 2-차원 바코드를 써서, 최대 2,953 개 길이의 ISO 8859-1 문자열로 '부호화 (encoding)' 된, 이름표를 사용합니다.

![2-d barcode](/assets/Swift/Swift-Programming-Launguage/Enumerations-2d-barcode.png)

'재고 물품 추적 시스템' 에서 UPC 바코드는 네 개의 정수로 된 '튜플' 로 저장하고, QR 코드 바코드는 임의 길이의 문자열로 저장하는 것이 편리합니다.

스위프트에서, 어느 타입도 가질 수 있는 '제품 바코드' 를 열거체로 정의하면 다음과 같을 것입니다:

```swift
enum Barcode {
  case upc(Int, Int, Int, Int)
  case qrCode(String)
}
```

이것은 다음 처럼 이해할 수 있습니다:

"`Barcode` 라는 열거체를 정의하는데, 이는 '결합된 값' 의 타입이 (`Int`, `Int`, `Int`, `Int`) 인 `upc` 값을 취하거나, '결합된 값' 의 타입이 `String` 인 `qrCode` 값을 취합니다."

이 정의는 실제로 어떠한 `Int` 나 `String` 값도 제공하지 않습니다-단지 `Barcode` 상수와 변수가 `Barcode.upc` 나 `Barcode.qrCode` 와 같을 때에 저장할 수 있는 '결합된 값 (associated values)' 의 _타입 (type)_ 만을 정의합니다.

이러면 어떤 타입을 사용하든 새 바코드를 생성할 수 있습니다:

```swift
var productBarcode = Barcode.upc(8, 85909, 51226, 3)
```

이 예제는 `productBarcode` 라는 새 변수를 생성하고 '결합된 튜플 값' 이 `(8, 85909, 51226, 3)` 인 `Barcode.upc` 라는 값을 할당합니다.

같은 제품에 다른 타입의 바코드를 할당할 수 있습니다:

```swift
productBarcode = .qrCode( "ABCDEFGHIJKLMNOP")
```

이 시점에서, 원래의 `Barcode.upc` 와 정수 값들은 새 `Barcode.qrCode` 와 문자열 값으로 대체됩니다. `Barcode` 타입의 상수와 변수는 `.upc` 나 `.qrCode` 라면 (그 결합된 값과 함께) 아무거나 저장할 수 있지만, 주어진 시점에는 이 중 단 하나만을 저장할 수 있습니다.

서로 다른 바코드 타입들은 'switch' 문을 사용하여 검사할 수 있는데, 이는 [Matching Enumeration Values with a Switch Statement ('switch' 문으로 열거체 값 맞춰보기)](#matching-enumeration-values-with-a-switch-statement-switch-문으로-열거체-값-맞춰보기) 에 있는 예제와 비슷합니다. 하지만, 이번에는 'switch' 문에서 '결합된 값' 을 뽑아내게 됩니다. 각각의 '결합된 값' 을 (`let` 접두사를 사용한) 상수나 (`var` 접두사를 사용한) 변수로 뽑아내서 `switch` 문의 'case 절' 본문에서 사용합니다:

```swift
switch productBarcode {
case .upc(let numberSystem, let manufacturer, let product, let check):
  print("UPC: \(numberSystem), \(manufacturer), \(product), \(check).")
case .qrCode(let productCode):
  print("QR code: \(productCode).")
}
// "QR code: ABCDEFGHIJKLMNOP." 를 출력합니다.
```

열거체 'case 값' 에 대한 모든 '결합된 값' 을 상수로 뽑아내거나, 아니면 변수로 뽑아낼 경우, `ver` 나 `let` 키워드를 'case 값' 이름 앞에 위치해서, 간결하게, 만들 수 있습니다:

```swift
switch productBarcode {
case let .upc(numberSystem, manufacturer, product, check):
  print("UPC : \(numberSystem), \(manufacturer), \(product), \(check).")
case let .qrCode(productCode):
  print("QR code: \(productCode).")
}
// "QR code: ABCDEFGHIJKLMNOP." 를 출력합니다.
```

### Raw Values (원시 값)

[Associated Values (결합된 값)](#associated-values-결합된-값) 에 있는 바코드 예제는 열거체의 'case 값' 이 서로 다른 타입의 '결합된 값' 을 저장한다고 선언할 수 있는 방법을 보여줍니다. '결합된 값' 에 대한 대안으로, '열거체 case 값' 은 모두 같은 타입인, (_원시 값 (raw values)_ 이라는) '기본 설정 값 (default values)' 으로 미리 채울 수 있습니다.

다음은 '열거체 case 값' 이 '이름' 과 '원시 ASCII 값' 을 같이 저장하고 있는 예제입니다:

```swift
enum ASCIIControlCharacter: Character {
  case tab = "\t"
  case lineFeed = "\n"
  case carriageReturn = "\r"
}
```

여기서, `ASCIIControlCharacter` 라는 열거체는 '원시 값 (raw values)' 은 `Character` 타입으로 정의되어, 좀 더 일상적인 ASCII 제어 문자들로 설정됩니다. `Character` 값은 [Strings and Characters (문자열과 문자)]({% post_url 2016-05-29-Strings-and-Characters %}) 에서 설명했습니다.

'원시 값' 은 '문자열 (strings)', '문자 (characters)', 또는 '정수 (integer)' 나 '부동-소수점 (floating-point)' 수치 타입 아무거나 될 수 있습니다. 각각의 원시 값은 '열거체 선언' 내에서 반드시 유일해야 합니다.

> '원시 값 (raw values)' 은 '결합된 값 (associated values)' 과 같지 _않습니다 (not)_. '원시 값' 은, 위의 세 ASCII 코드에서와 같이, 미리 채워지는 값으로 코드에서 열거체를 처음 정의할 때 설정되는 것입니다. 즉 특정한 '열거체 case 값' 에 대한 '원시 값' 은 항상 같습니다. '결합된 값' 은 열거체의 'case 값' 중 하나를 기반으로 새로운 상수나 변수를 생성할 때 설정되는 것으로, 그렇게 할 때마다 달라질 수 있습니다.

#### Implicitly Assigned Raw Values (암시적으로 할당되는 원시 값)

정수나 문자열 원시 값을 저장하는 열거체와 작업할 때는, 각각의 'case 값' 에 '원시 값' 을 명시적으로 지정하지 않아도 됩니다. 이렇 경우, 스위프트가 자동으로 값을 할당합니다.

예를 들어, 원시 값으로 정수를 사용할 때는, 각 'case 값' 에 대한 암시적인 값은 이전 'case 값' 보다 하나 큰 값이 됩니다. 첫 번째 'case 값' 에 값을 설정하지 않으면, 그 값은 `0` 이 됩니다.

아래의 열거체는 이전에 있던 `Planet` 열거체를, 태양에 대한 각 행성의 순서를 표현하기 위해 정수 원시 값을 사용하여, 개량한 것입니다:

```swift
enum Planet: Int {
  case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
}
```

위 예제에서, `Planet.mercury` 는 명시적인 원시 값 `1` 을 가지고, `Planet.venus` 는 암시적인 원시 값 `2` 를 가지며, 이런 식으로 계속됩니다.

문자열을 원시 값으로 사용하면, 'case 값' 이름에 사용된 문장 자체가 각 'case 값' 에 대한 암시적인 값이 됩니다.

아래 열거체는, 문자열 원시 값으로 각 방향의 이름을 표현하도록, 이전 `CompassPoint` 열거체를 개량한 것입니다:

```swift
enum CompassPoint: String {
  case north, south, east, west
}
```

위 예제에서, `CompassPoint.south` 는 암시적인 원시 값 `"south"` 를 가지며, 이런 식으로 계속됩니다.

열거체 case 값의 원시 값에 접근하려면 `rawValue` 속성을 사용하면 됩니다:

```swift
let earthsOrder = Planet.earth.rawValue
// earthsOrder (지구의 순번) 은 3 입니다.

let sunsetDirection = CompassPoint.west.rawValue
// sunsetDirection (해가 지는 방향) 은 "west" (서쪽) 입니다.
```

#### Initializing from a Raw Value (원시 값으로 초기화하기)

열거체에 원시-값 타입을 정의하면, 이 열거체는 자동적으로 원시 값 타입의 값을 (`rawValue` 라는 매개 변수로) 받는 '초기자' 를 가지며 하나의 열거체 'case 값' 또는 `nil` 을 반환하게 됩니다. 이 '초기자' 를 사용하면 열거체의 새 인스턴스를 생성할 수 있습니다.

다음 예제는 '원시 값' 인 `7` 로 부터 '천왕성 (Uranus)' 을 식별합니다:

```swift
let possiblePlanet = Planet(rawValue: 7)
// possiblePlanet 은 타입이 Planet? 이고 값은 Planet.uranus 입니다.
```

하지만, 모든 `Int` 에 대해 그에 해당하는 행성을 찾을 수 있는 것은 아닙니다. 이 때문에, '_원시 (raw)_ 값 초기자' 는 항상 _옵셔널 (optional)_ 열거체 'case 값' 을 반환합니다. 위의 예제에서, `possiblePlanet` 의 타입은 `Planet?`, 또는 “옵셔널 (optional) `Planet`” 입니다.

> '원시 값 초기자 (raw value initializer)' 는 '실패 가능한 초기자 (failable initializer)' 인데, 모든 원시 값이 열거체 case 값을 반환하지는 않기 때문입니다. 더 자세한 내용은, [Failable Initializers (실패 가능한 초기자)](https://docs.swift.org/swift-book/ReferenceManual/Declarations.html#ID376)[^failable-initializer] 를 참고하기 바랍니다.

위치가 `11` 에 해당하는 행성을 찾으려고 하면, '원시 값 초기자 (raw value initializer)' 가 반환하는 '옵셔널 (optional)' `Planet` 값은 `nil` 이 될 것입니다:

```swift
let positionToFind = 11
if let somePlanet = Planet(rawValue: positionToFind) {
  switch somePlanet {
  case .earth:
    print("Mostly harmless")
  default:
    print("Not a safe place for humans")
  }
} else {
  print("There isn't a planet at position \(positionToFind)")
}
// "There isn't a planet at position 11" 를 출력합니다.
```

이 예제는 '옵셔널 연결 (optional binding)' 을 사용하여 원시 값이 `11` 인 행성에 접근하려고 시도합니다. `if let somePlanet = Planet(rawValue : 11)` 구문은 옵셔널 `Planet` 을 하나 생성한 다음, 가져올 수 있다면 이 옵셔널 `Planet` 의 값을 `somePlanet` 에 설정합니다. 이 경우, 위치가 `11` 인 행성은 가져올 수 없으므로, `else` 분기를 대신 실행합니다.

### Recursive Enumerations (재귀적인 열거체)

_재귀적인 열거체 (recursive enumeration)_ 는 열거체의 'case 값' 들이 열거체의 또 다른 인스턴스를 '결합된 값 (associated value)' 으로 하나 이상 가지는 열거체를 말합니다. 열거체의 'case 값' 을 '재귀적' 이라고 지시하려면 그 앞에 `indirect` [^indirect]를 써주면 되는데, 이는 컴파일러에게 간접 계층을 집어넣어야 함을 알리는 역할을 합니다.

예를 들어, 다음은 간단한 '산술 표현식 (arithmetic expressions)' 을 저장하는 열거체입니다:

```swift
enum ArithmeticExpression {
  case number(Int)
  indirect case addition(ArithmeticExpression, ArithmeticExpression)
  indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

열거체 맨 처음에 `indirect` 를 붙이면 '결합된 값' 이 있는 모든 열거체 'case 값' 을 한번에 '간접 (indirection)'[^indirection] 으로 만들 수도 있습니다:

```swift
indirect enum ArithmeticExpression {
  case number(Int)
  case addition(ArithmeticExpression, ArithmeticExpression)
  case multiplication(ArithmeticExpression, ArithmeticExpression)
}
```

이 열거체는 세 가지 종류의 '산술 표현식' 을 저장할 수 있습니다: 하나의 단순한 수, 두 표현식의 덧셈, 그리고 두 표현식의 곱셈이 그것입니다. `addition` 과 `multiplication` 'case 값' 은 그 자체도 '산술 표현식 (arithmetic expression)' 인 '결합된 값 (associated values)' 을 가지고 있습니다-이 '결합된 값' 은 표현식을 '중첩 (nest)' 하는 것을 가능하게 만듭니다. 예를 들어, 표현식 `(5 + 4) * 2` 의 경우 곱셈 오른 쪽은 숫자를 가지고 있고 곱셈 왼 쪽은 또 다른 표현식을 가지고 있습니다. 자료가 중첩되기 때문에, 이 자료를 저장할 열거체 역시 중첩을 지원해야 합니다-이는 열거체가 '재귀적 (recursive)' 일 필요가 있다는 것을 의미합니다. 아래의 코드는 재귀적인 열거체인 `ArithmeticExpression` 를 생성하여 `(5 + 4) * 2` 를 만드는 것을 보여줍니다:

```swift
let five = ArithmeticExpression.number(5)
let four = ArithmeticExpression.number(4)
let sum = ArithmeticExpression.addition(five, four)
let product = ArithmeticExpression.multiplication(sum, ArithmeticExpression.number(2))
```

'재귀 함수 (recursive function)' 는 재귀 구조를 가지는 자료와 작업하기 수월한 방법입니다. 예를 들어, 다음은 '산술 표현식' 의 값을 계산하는 함수입니다:

```swift
func evaluate(_ expression: ArithmeticExpression) -> Int {
  switch expression {
  case let .number(value):
    return value
  case let .addition(left, right):
    return evaluate(left) + evaluate(right)
  case let .multiplication(left, right):
    return evaluate(left) * evaluate(right)
  }
}

print(evaluate(product))
// "18" 를 출력합니다.
```

이 함수는 '하나의 단순한 수' 일 경우 그냥 그 '결합된 값' 을 반환하는 것으로 계산을 끝냅니다. 덧셈과 곱셈은 왼쪽 표현식을 계산한 다음, 오른쪽 표현식을 계산하고, 이들을 더하거나 곱하는 것으로 계산합니다.

### 참고 자료

[^Enumerations]: 이 글에 대한 원문은 [Enumerations](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html) 에서 확인할 수 있습니다.

[^type-safe]: 여기서 '타입-안전한 방식 (type-safe way)' 이라는 것은 스위프트가 기본적으로 제공하는 '타입 추론 (type inference)' 과 '타입 검사 (type check)' 기능을 사용할 수 있다는 것을 의미합니다. 이 내용은 [The Basic (기초)]({% post_url 2016-04-24-The-Basics %}) 부분의 [Type Safety and Type Inference (타입 안전 장치와 타입 추론 장치)]({% post_url 2016-04-24-The-Basics %}#type-safety-and-type-inference-타입-안전-장치와-타입-추론-장치) 에서 설명한 바 있습니다.

[^first-class]: 프로그래밍에서 '일급 (first-class)' 이라는 말은 특정 대상을 '객체' 와 동급으로 사용할 수 있다는 것을 의미합니다. 예를 들어 '객체' 처럼 인자로 전달할 수도 있고, 함수에서 반환할 수 있으며, 다른 변수 등에 할당할 수도 있는 대상이 있다면 이 대상을 '일급 (first-class)' 이라고 합니다. 이에 대한 보다 자세한 내용은 위키피디아의 [First-class citizen](https://en.wikipedia.org/wiki/First-class_citizen) 과 [일급 객체](https://ko.wikipedia.org/wiki/일급_객체) 항목을 참고하기 바랍니다.

[^variants]: 이 세가지 개념은 사실상 같은 것으로, 각각은 위키피디아의 [Tagged union](https://en.wikipedia.org/wiki/Tagged_union), [Variant type](https://en.wikipedia.org/wiki/Variant_type) 항목을 참고하기 바랍니다. 참고로 컴퓨터 공학에서는 'discriminated union' 가 'tagged union' 을 의미한다고 하며 이 둘은 따로 항목이 나뉘지 않습니다.

[^failable-initializer]: 사실 해당 내용은 **Language Guide** 부분의 [Initialization (초기화)]({% post_url 2016-01-23-Initialization %}) 에 있는 [Failable Initializers (실패 가능한 초기자)]({% post_url 2016-01-23-Initialization %}#failable-initializers-실패-가능한-초기자) 와 [Failable Initializers for Enumerations with Raw Values (원시 값을 가지는 열거체를 위한 실패 가능한 초기자)]({% post_url 2016-01-23-Initialization %}#failable-initializers-for-enumerations-with-raw-values-원시-값을-가지는-열거체를-위한-실패-가능한-초기자) 에서도 설명하고 있습니다.

[^indirect]: 여기서 '재귀적인 (recursive)' 열거체를 만들기 위해 `indirect` 라는 키워드를 사용하고 있는데, 이는 메모리 주소 방식 중 하나인 'indirect addressing mode' 에서 온 개념으로 추측됩니다. 'indirect addressing mode' 에 대한 보다 더 자세한 내용은 [Difference between Indirect and Immediate Addressing Modes](https://www.geeksforgeeks.org/difference-between-indirect-and-immediate-addressing-modes/?ref=rp) 를 참고하기 바랍니다.

[^indirection]: 본문을 보면 '재귀적 (recursive)' 이라는 말과 '간접 (indirection)' 이라는 말을 거의 같은 개념으로 사용하고 있는데, 이는 스위프트 열거체를 '재귀적' 으로 만드는 방식이 내부적으로는 메모리의 '간접 주소' 방식을 써서 구현했기 때문으로 추측됩니다. 물론 스위프트 프로그래밍을 위해 이런 걸 알아야 하는 것은 아니므로 그런게 있다고 넘어가면 될 것 같습니다.
