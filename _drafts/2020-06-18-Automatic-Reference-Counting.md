---
layout: post
comments: true
title:  "Swift 5.3: Automatic Reference Counting (자동 참조 카운팅)"
date:   2020-06-18 10:00:00 +0900
categories: Swift Language Grammar ARC Automitic Reference Counting
---

> Apple 에서 공개한 [The Swift Programming Language (Swift 5.3)](https://docs.swift.org/swift-book/) 책의 [Automatic Reference Counting](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html) 부분[^Automatic-Reference-Counting]을 번역하고, 설명이 필요한 부분은 주석을 달아서 정리한 글입니다.
>
> 현재 번역이 진행 중인데, 2020/06/22 에 Swift 5.3 이 발표되어, 이미 번역된 부분과 남은 부분 모두 Swift 5.3 을 기준으로 옮기도록 합니다. 완료된 목록은 [Swift 5.3: Swift Programming Language (스위프트 프로그래밍 언어)]({% post_url 2017-02-28-The-Swift-Programming-Language %}) 에서 확인할 수 있으며, 일부는 Swift 5.2 기준일 수 있습니다.

## Automatic Reference Counting (자동 참조 카운팅)

스위프트는 앱의 메모리 사용량을 추적하고 관리하기 위해 _자동 참조 카운팅 (Automatic Reference Counting; ARC)_ 을 사용합니다. 이것은, 대부분의 경우, 스위프트에서 메모리 관리란 "그냥 작동하는 것 (just works)" 이라서, 직접 메모리 관리에 대해서 생각할 필요는 없다는 것을 의미합니다. ARC 는 클래스 인스턴스가 더 이상 필요하지 않을 경우 그 인스턴스가 사용하고 있는 메모리를 해제하고 확보합니다.

하지만, ARC 가 메모리 관리를 하려면 코드 간의 관계에 대한 더 많은 정보가 필요한 경우가 몇 가지 정도 있습니다. 이번 장에서는 이러한 상황을 설명하고 어떻게 하면 ARC 가 모든 앱의 메모리를 관리하게 만들 수 있는지를 보여줍니다. 스위프트에서 ARC 를 사용하는 것은 오브젝티브-C 에서 ARC 를 사용하기 위해 [Transitioning to ARC Release Notes](https://developer.apple.com/library/archive/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html)[^ARC-Objective-C] 에서 설명한 접근법과 아주 비슷합니다.

'참조 카운팅 (reference counting)' 은 클래스의 인스턴스에만 적용됩니다.[^reference-type] 구조체와 열거체는, 참조 타입이 아니라, 값 타입이라서, 참조의 형태로 저장되거나 전달되지 않습니다.

### How ARC Works (ARC 의 작동 방식)

클래스에 대한 인스턴스를 새로 생성할 때마다, ARC 는 해당 인스턴스에 대한 정보를 저장하기 위해 메모리 덩어리를 할당합니다. 이 메모리는 해당 인스턴스의 타입 정보 및, 그 인스턴스와 결합된 모든 저장 속성의 값들을 함께 가지고 있습니다.

여기다, 인스턴스가 더 이상 필요하지 않을 경우, ARC 가 해당 인스턴스가 사용하고 있는 메모리를 해제하여 확보하므로 그 메모리를 다른 용도로 사용할 수 있습니다. 이는 클래스 인스턴스가 더 이상 필요없는 경우 메모리 공간을 차지하지 않음을 보장해 줍니다.

하지만, ARC 가 아직 사용중인 인스턴스의 할당을 해제할 경우, 해당 인스턴스의 속성에 접근하거나, 해당 인스턴스의 메소드를 호출하는 것은 더 이상 가능하지 않을 것입니다. 진짜로, 그 인스턴스에 접근하려고 할 경우, 거의 대부분 앱이 충돌할 것입니다.

아직 필요한 동안에 해당 인스턴스를 사라지지 않게 하기 위해, ARC 는 각각의 클래스 인스턴스를 현재 몇 개의 속성, 상수, 그리고 변수들이 참조하는지 추적합니다. ARC 는 해당 인스턴스에 대한 '활성화된 (active)' 참조가 하나라도 존재한다면 인스턴스의 할당을 해제하지 않습니다.

이를 가능하게 하기 위해, 클래스 인스턴스를 속성, 상수, 또는 변수에 할당할 때마다, 해당 속성, 상수, 또는 변수는 그 인스턴스에 대한 _강한 참조 (strong reference)_ 를 만듭니다. 이 참조를 "강한 (strong)" 참조라고 하는 이유는 이것이 해당 인스턴스를 꽉 움켜쥐고 있어서, 해당 '강한 참조' 가 남아 있는 한 할당이 해제되는 것을 허용하지 않기 때문입니다.

### ARC in Action (ARC 의 실제 사례)

다음은 '자동 참조 카운팅 (Automatic Reference Counting)' 이 어떻게 작동하는 지를 보여주는 예제입니다. 이 예제는, `name` 이라는 '상수 저장 속성'[^stored-constant-property] 을 정의하는, `Person` 이라는 간단한 클래스로 시작합니다:

```swift
class Person {
  let name: String
  init(name: String) {
    self.name = name
    print("\(name) is being initialized")
  }
  deinit {
    print("\(name) is being deinitialized")
  }
}
```

`Person` 클래스가 가지고 있는 '초기자' 는 인스턴스의 `name` 속성을 설정하고 초기화가 진행 중임을 나타내는 메시지를 출력합니다. `Person` 클래스는 이 클래스 인스턴스의 할당을 해제할 때 메시지를 출력하는 '정리자 (deinitializer)' 도 가지고 있습니다.

다음의 코드 조각은 타입이 `Person?` 인 변수를 세 개 정의하는데, 이어지는 코드 조각에서 새로운 `Person` 인스턴스에 대한 '다중 참조 (multiple references)'[^multiple-references] 를 설정하는데 사용됩니다. 이 변수들은 옵셔널 타입이기 때문에 (`Person` 이 아니라, `Person?`), 자동적으로 `nil` 값으로 초기화되며, 현재는 `Person` 인스턴스를 참조하고 있지 않습니다.

```swift
var reference1: Person?
var reference2: Person?
var reference3: Person?
```

이제 새로운 `Person` 인스턴스를 생성하여 세 변수 중 하나에 할당할 수 있습니다:

```swift
reference1 = Person(name: "John Appleseed")
// "John Appleseed is being initialized" 를 출력합니다.
```

`Person` 클래스의 '초기자' 를 호출하는 순간 `"John Appleseed is being initialized"` 라는 메시지를 출력하는 점에 주목하기 바랍니다. 이는 초기화가 일어났음을 입증하는 것입니다.

새 `Person` 인스턴스를 `reference1` 변수에 할당했기 때문에, 이제 `reference1` 에서 새 `Person` 인스턴스로 향하는 '강한 참조 (strong reference)' 가 생겼습니다. 최소 하나의 '강한 참조' 가 있으므로, ARC 는 이 `Person` 에 대한 메모리를 유지하고 할당을 해제하지 않습니다.

똑같은 `Person` 인스턴스를 두 변수에 더 할당할 경우, 해당 인스턴스에 대한 두 개의 '강한 참조' 가 더 생기게 됩니다:

```swift
reference2 = reference1
reference3 = reference1
```

이제 단일한 `Person` 인스턴스에 대한 _세 개의 (three)_ '강한 참조' 가 있습니다.

두 변수에 `nil` 을 할당하여 세 개의 '강한 참조' 중 (원래의 참조를 포함하여) 두 개를 끊더라도, '강한 참조' 한 개는 남아 있으므로, `Person` 인스턴스의 할당은 해제되지 않습니다:

```swift
reference1 = nil
reference2 = nil
```

ARC 는 세 번째이자 마지막인 '강한 참조' 를 끊을 때까지, 즉 `Person` 인스턴스를 더 이상 사용하지 않는 것이 분명할 때까지, `Person` 의 할당을 해제하지 않습니다:

```swift
reference3 = nil
// "John Appleseed is being deinitialized" 를 출력합니다.
```

### Strong Reference Cycles Between Class Instances (클래스 인스턴스 사이의 강한 참조 순환)

위 예제에서, ARC 는 새로 생성한 `Person` 인스턴스의 참조 개수를 추적하여 더 이상 필요하지 않을 때 `Person` 인스턴스의 할당을 해제할 수 있었습니다.

하지만, 클래스 인스턴스에 대한 '강한 참조' 가 _절대로 (never)_ 없어지지 않는 코드를 작성하게 될 수도 있습니다. 이것은 두 클래스 인스턴스가 서로에 대한 '강한 참조' 를 움켜쥐고 있는 경우에 발생하는데, 그로 인해 각각의 인스턴스가 다른 것을 계속 살아있게 만드는 것입니다. 이를 '_강한 참조 순환 (strong reference cycle)_' 이라고 합니다.

'강한 참조 순환' 을 해결하려면 클래스 사이의 일부 관계를 강한 참조가 아니라 '약한 참조 (weak reference)' 나 '소유자가 없는 참조 (unowned rerference)' 로 정의해야 합니다. 이 과정은 [Resolving Strong Reference Cycles Between Class Instances (클래스 인스턴스 사이의 강한 참조 순환 해결하기)](#resolving-strong-reference-cycles-between-class-instances-클래스-인스턴스-사이의-강한-참조-순환-해결하기) 에서 설명합니다. 하지만, 강한 참조 순환을 해결하는 방법을 배우기 전에, 어떻게 해서 그런 순환이 일어나는지를 먼저 이해하는 것이 좋을 겁니다.

다음은 강한 참조 순환이 어떻게 해서 우연히 생성될 수 있는 지를 보여주는 예제입니다. 이 예제는, 아파트 단지 및 그 거주자를 모델링하는, `Person` 과 `Apartment` 라는 두 개의 클래스를 정의합니다:

```swift
class Person {
  let name: String
  init(name: String) { self.name = name }
  var apartment: Apartment?
  deinit { print("\(name) is being deinitialized") }
}

class Apartment {
  let unit: String
  init(unit: String) { self.unit = unit }
  var tenant: Person?
  deinit { print("Apartment \(unit) is being deinitialized") }
}
```

모든 `Person` 인스턴스는 `String` 타입인 `name` 속성과 `nil` 로 초기화된 옵셔널 `apartment` 속성을 가지고 있습니다. `apartment` 속성이 '옵셔널' 인건, 사람이 항상 아파트를 가지고 있는 건 아니기 때문입니다.

이와 비슷하게, 모든 `Apartment` 인스턴스는 `String` 타입인 `unit` 속성을 가지며 `nil` 로 초기화된 옵셔널 `tenant` 속성도 가지고 있습니다. '입주자 (tenant)' 속성이 옵셔널인 것은 아파트에 항상 입주자가 있는 것은 아니기 때문입니다.

이 두 클래스 모두, 해당 클래스의 인스턴스가 정리되었다는 사실을 출력하는, '정리자 (deinitializer)'[^deinitializer] 도 정의하고 있습니다. 이는 `Person` 과 `Apartment` 의 인스턴스에 대한 할당이 예상대로 해제되고 있는지를 확인할 수 있도록 해줍니다.

다음 코드 조각은 `john` 과 `unit4A` 라는 옵셔널 타입의 값을 두 개 정의하는데, 이는 그 아래에서 특정한 `Apartment` 와 `Person` 인스턴스로 설정하게 됩니다. 이 두 변수 모두, 옵셔널이 가지는 장점에 의해, `nil` 이라는 초기 값을 가집니다.

```swift
var john: Person?
var unit4A: Apartment?
```

이제 특정한 `Person` 인스턴스와 `Apartment` 인스턴스를 생성해서 이 새 인스턴스를 `john` 과 `unit4A` 변수에 할당할 수 있습니다:

```swift
john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")
```

다음은 두 인스턴스를 생성하고 할당한 후 '강한 참조' 가 어떻게 보이는지를 나타냅니다. `john` 변수는 이제 새 `Person` 인스턴스에 대한 '강한 참조' 를 가지고 있고, `unit4A` 변수는 새 `Apartment` 인스턴스에 대한 '강한 참조' 를 가지고 있습니다:

![Strong Reference Start](/assets/Swift/Swift-Programming-Language/Automatic-Reference-Counting-strong-before.jpg)

이제 두 인스턴스를 서로 연결하여 사람은 아파트를 가지고, 아파트는 입주자를 가지게 할 수 있습니다. `john` 과 `unit4A` 옵셔널 변수 안에 저장된 인스턴스를 풀고 접근하기 위해서, 그래서 그 속성을 설정할 수 있도록 하기 위해서, 느낌표 (`!`) 를 사용한 것을 기억하기 바랍니다:

```swift
john!.apartment = unit4A
unit4A!.tenant = john
```

다음은 두 인스턴스를 서로 연결하고 난 후 '강한 참조' 가 어떻게 보이는지를 나타냅니다:

![Strong Reference](/assets/Swift/Swift-Programming-Language/Automatic-Reference-Counting-strong-reference.png)

불행하게도, 이 두 인스턴스를 연결하는 것은 이들 사이에 '강한 참조 순환 (strong reference cycle)' 을 생성합니다. `Person` 인스턴스는 이제 `Apartment` 인스턴스에 대한 '강한 참조' 를 가지며, `Apartment` 인스턴스는 `Person` 인스턴스에 대한 '강한 참조' 를 가지게 됩니다. 따라서, `john` 과 `unit4A` 변수가 움켜쥐고 있던 '강한 참조' 를 끊더라도, '참조 카운트 (reference counts)' 는 0 으로 떨어지지 않으며, 인스턴스의 할당은 ARC 에 의해 해제되지 않습니다:

```swift
john = nil
unit4A = nil
```

이 두 변수를 `nil` 로 설정할 때 어느 '정리자 (deinitializer)' 도 호출되지 않는다는 점을 기억하기 바랍니다. '강한 참조 순환' 는 `Person` 과 `Apartment` 인스턴스의 할당이 제되는 것을 막아서, 앱에서 '메모리 누수 (memory leak)' 를 발생시킵니다.

다음은 `john` 과 `unit4A` 변수에 `nil` 을 설정한 후 '강한 참조' 가 어떻게 보이는지를 나타냅니다:

![Strong Reference Remaining](/assets/Swift/Swift-Programming-Language/Automatic-Reference-Counting-strong-remain.png)

`Person` 인스턴스와 `Apartment` 인스턴스 사이에 있는 '강한 참조' 는 깨지지 않고 남아 있습니다.

### Resolving Strong Reference Cycles Between Class Instances (클래스 인스턴스 사이의 강한 참조 순환 해결하기)

스위프트는 클래스 타입의 속성과 작업할 때 '강한 참조 순환 (strong reference cycle)' 을 해결하는 방법으로 두 가지를 제공합니다: '약한 참조 (weak reference)' 와 '소유자가 없는 참조 (unowned reference)' 입니다.

'약한 참조' 와 '소유자가 없는 참조' 는 '참조 순환' 에 있는 인스턴스 중 하나가 다른 인스턴스를 강하게 움켜쥐지 _않고도 (without)_ 참조할 수 있도록 해줍니다. 그러면 이 인스턴스들은 '강한 참조 순환' 을 생성하지 않고도 서로를 참조할 수 있습니다.

'약한 참조' 는 다른 인스턴스의 일생이 더 짧을 때 사용합니다-다시 말해서, 다른 인스턴스의 할당이 먼저 해제되는 경우 사용합니다. 위의 `Apartment` 예제에서, 일생 중 어느 시점에 입주자가 없을 수 있다는 것은 아파트 입장에서 적절한 것이므로, '약한 참조' 로 '참조 순환' 을 끊는 것은 이 경우 적절한 방법에 해당합니다. 이와는 대조적으로, '소유자가 없는 참조' 는 다른 인스턴스와 일생이 같거나 더 길 때 사용합니다.

#### Week References (약한 참조)

_약한 참조 (weak reference)_ 는 참조하는 인스턴스를 강하게 움켜쥐지 않는 참조라서, ARC 가 참조된 인스턴스를 처리하는 것을 중지하지 않습니다. 이런 동작 방식은 참조가 '강한 참조 순환' 의 일부가 되는 것을 막아줍니다. '약한 참조' 를 지시하려면 `weak` 키워드를 속성이나 변수의 선언 앞에 붙이면 됩니다.

약한 참조는 참조하고 있는 인스턴스를 강하게 움켜쥐지 않기 때문에, 약한 참조가 아직 참조하고 있는 중에도 해당 인스턴스의 할당을 해제하는 것이 가능합니다. 따라서, ARC 는 참조하고 있는 해당 인스턴스의 할당을 해체할 때 자동적으로 '약한 참조' 를 `nil` 로 설정합니다. 그리고, 약한 참조는 실행 시간에 값을 `nil` 로 바꿀 수 있어야 하므로, 항상 옵셔널 타입이며, 상수가 아니라, 변수로 선언되어야 합니다.

약한 참조는, 다른 모든 옵셔널 값들 처럼, 값이 존재하는 지를 검사할 수 있으며, 더 이상 존재하지 않는 무효한 인스턴스에 대한 참조로 끝나서는 절대로 안됩니다.

> ARC 가 약한 참조를 `nil` 로 설정할 때는 '속성 관찰자 (property observers)' 가 호출되지 않습니다.

아래 예제는 위에 있는 `Person` 및 `Apartment` 예제와 모든 점에서 똑같지만, 한 가지 중요한 차이점이 있습니다. 이번에는, `Apartment` 타입의 `tenat` 속성을 '약한 참조' 로 선언했습니다:

```swift
class Person {
  let name: String
  init(name: String) { self.name = name }
  var apartment: Apartment?
  deinit { print("\(name) is being deinitialized") }
}

class Apartment {
  let unit: String
  init(unit: String) { self.unit = unit }
  weak var tenant: Person?
  deinit { print("Apartment \(unit) is being deinitialized") }
}
```

두 변수 (`john` 과 `unit4A`) 로부터의 '강한 참조' 와 두 인스턴스 사이의 '연결 고리 (link)' 는 이전 처럼 생성됩니다:

```swift
var john: Person?
var unit4A: Apartment?

john = Person(name: "John Appleseed")
unit4A = Apartment(unit: "4A")

john!.apartment = unit4A
unit4A!.tenant = john
```

다음은 두 인스턴스를 서로 연결하고 난 후 '참조' 가 이제 어떻게 보이는지를 나타냅니다:

![Weak Reference](/assets/Swift/Swift-Programming-Language/Automatic-Reference-Counting-weak-reference.jpg)

`Person` 인스턴스는 여전히 `Apartment` 인스턴스에 대한 '강한 참조' 를 가지고 있지만, `Apartment` 인스턴스는 이제 `Person` 인스턴스에 대한 '_약한 (weak)_ 참조' 를 가지고 있습니다. 이것의 의미는 `john` 변수를 `nil` 로 설정하여 이 변수가 강하게 움켜쥐고 있던 참조를 깨뜨릴 경우, 이제 `Person` 인스턴스에 대한 '강한 참조' 는 더 이상 없다는 것입니다.

```swift
john = nil
// "John Appleseed is being deinitializaed" 를 출력합니다.
```

`Person` 인스턴스에 대한 강한 참조가 이제 더 이상 없으므로, 이의 할당은 해제되고 `tenant` 속성은 `nil` 로 설정됩니다:

![Weak Reference nil](/assets/Swift/Swift-Programming-Language/Automatic-Reference-Counting-weak-nil.jpg)

`Apartment` 인스턴스에 대해 남아있는 유일한 '강한 참조' 는 `unit4A` 변수에 의한 것입니다. 만약 _그 (that)_ 강한 참조를 깨뜨리면, `Apartment` 인스턴스에 대한 '강한 참조' 는 이제 더 이상 없습니다:

```swift
unit4A = nil
// "Apartment 4A is being deinitialized" 를 출력합니다.
```

`Apartment` 인스턴스에 대한 강한 참조가 이제 더 이상 없으므로, 이것의 할당 역시 해제됩니다:

![Weak Reference deallocated](/assets/Swift/Swift-Programming-Language/Automatic-Reference-Counting-weak-deallocated.jpg)

> '쓰레기 수집 (gabage collection)'[^gabage-collection] 을 사용하는 시스템에서는, 간단한 '캐싱 메커니즘 (caching mechanism)' 을 구현하기 위해 '약한 참조' 를 사용할 때가 있는데 이는 '메모리 압력' 이 '쓰레기 수집' 을 발생시키는 경우에만 '강한 참조' 가 없는 객체의 할당이 해제되기 때문입니다. 하지만, ARC 에서는, 마지막 '강한 참조' 가 제거되자 마자 값의 할당이 해제되므로, 약한 참조를 그런 용도로 사용하는 것은 적합하지 않습니다.

#### Unowned References (소유자가 없는 참조)

'약한 참조' 와 마찬가지로, _소유자가 없는 참조 (unowned reference)_ 는 참조하는 인스턴스를 강하게 움켜쥐고 있지 않습니다. 하지만, 약한 참조와는 다르게, '소유자가 없는 참조' 는 다른 인스턴스가 같은 수명 또는 더 긴 수명을 가지고 있을 때 사용합니다. '소유자가 없는 참조' 를 지시하려면 `unowned` 키워드를 속성이나 변수 선언 앞에 붙이면 됩니다.

'소유자가 없는 참조' 는 항상 값이 있다고 예상합니다. 그 결과로, ARC 는 '소유자가 없는 참조' 의 값을 절대로 `nil` 로 설정하지 않으며, 이는 '소유자가 없는 참조' 는 옵셔널이-아닌 타입을 사용하여 정의된다는 것을 의미합니다.

> 해당 참조가 할당이 해제되지 않은 인스턴스만 _항상 (always)_ 참조할 것임을 확신할 수 있을 때만 '소유자가 없는 참조' 를 사용하기 바랍니다.
>
> 인스턴스의 할당이 해제된 후 '소유자가 없는 참조' 의 값에 접근하려고 하면, '실행시간 에러 (runtime error)' 가 발생할 것입니다.

다음 예제는, '은행 고객' 과 그 고객이 가질 '신용 카드' 를 모델링하기 위해, `Customer` 와 `CreditCard` 라는, 두 개의 클래스를 정의합니다. 이 두 클래스는 각자 서로의 클래스 인스턴스를 속성으로 저장합니다. 이러한 관계는 잠재적으로 '강한 참조 순환' 를 생성하게 됩니다.

`Customer` 와 `CreditCard` 의 관계는 위의 '약한 참조' 예제에서 봤었던 `Apartment` 와 `Person` 의 관계와는 조금 다릅니다. 이 자료 모델에서, 고객은 신용 카드를 가질 수도 있고 가지지 않을 수도 있지만, 하나의 신용 카드는 _항상 (always)_ 한 명의 고객과 연결되어 있을 것입니다. `CreditCard` 인스턴스는 자신이 참조하는 `Customer` 보다 절대로 더 오래 살지 않습니다. 이를 표현하기 위해, `Customer` 클래스는 `card` 라는 옵셔널 속성을 가지지만, `CreditCard` 클래스는 '소유자가 없는 (그리고 옵셔널이-아닌)' `customer` 속성을 가집니다.

더 나아가서, 새 `CreditCard` 인스턴스는 _오직 (only)_ `number` 값과 `custom` 인스턴스를 자체 `CreditCard` 초기자로 전달하는 경우에만 생성할 수 있습니다. 이는 `CreditCard` 인스턴스를 생성할 때 `CreditCard` 인스턴스가 항상 자신과 결합된 `customer` 인스턴스를 가지고 있음을 보장합니다.

신용 카드는 항상 고객을 가지고 있기 때문에, `customer` 인스턴스를 '소유자가 없는 참조' 로 정의하여, '강한 참조 순환' 을 피할 수 있습니다:

```swift
class Customer {
  let name: String
  var card: CreditCard?
  init(name: String) {
    self.name = name
  }
  deinit { print("\(name) is being deinitialized") }
}

class CreditCard {
  let number: UInt64
  unowned let customer: Customer
  init(number: UInt64, customer: Customer) {
    self.number = number
    self.customer = customer
  }
  deinit { print("Card #\(number) is being deinitialized") }
}
```

> `CreditCard` 클래스의 `number` 속성은 `Int` 가 아니라 `UInt64` 타입으로 정의했는데, 이는 `number` 속성의 용량이 32-비트와 64-비트 시스템 모두에서 16-자리 카드 번호를 저장할 만큼 크도록 보장해 줍니다.

이 다음 코드 조각은, 특정 고객에 대한 참조를 저장하는데 사용할, `john` 이라는 옵셔널 `Customer` 변수를 정의합니다. 이 변수는, 옵셔널이 가진 장점에 따라, 'nil' 을 초기 값으로 가집니다:

```swift
var john: Customer?
```

이제 `Customer` 인스턴스를 생성하여, 새로운 `CreditCard` 인스턴스를 초기화하고 해당 고객의 `card` 속성으로 할당하는데 사용합니다:

```swift
john = Customer(name: "John Appleseed")
john!.card = CreditCard(number: 1234_5678_9012_3456, customer: john!)
```

다음은 두 인스턴스를 서로 연결하고 난 후, 이제 '참조' 가 어떻게 보이는지를 나타냅니다:

#### Unowned References and Implicitly Unwrapped Optional Properties (소유자가 없는 참조 및 암시적으로 풀리는 옵셔널 속성)

### Strong Reference Cycles for Closures (클로저에 대한 강한 참조 순환)

### Resolving Strong Reference Cycles for Closures (클로저에 대한 강한 참조 순환 해결하기)

#### Defining a Capture List (붙잡을 목록 정의하기)

#### Weak and Unowned References (약한 참조와 소유자가 없는 참조)

### 참고 자료

[^Automatic-Reference-Counting]: 이 글에 대한 원문은 [Automatic-Reference-Counting](https://docs.swift.org/swift-book/LanguageGuide/AutomaticReferenceCounting.html) 에서 확인할 수 있습니다.

[^ARC-Objective-C]: 해당 내용은 오브젝티드-C 개발자를 위한 내용을, 원문 자체에서 이미 웹페이지로 연결되도록 링크를 달아 놓은 것입니다. 오브젝티브-C 개발자가 아니라면 다 이해할 필요는 없을 것입니다.

[^reference-type]: '참조 카운팅 (reference counting)' 은 스위프트의 메모리 관리 방법으로, 여기서 '메모리 관리' 는 '동적인 메모리를 자동으로 할당하고 해제하는 것' 을 의미합니다. 프로그래밍에서 '동적인 메모리' 의 할당, 해제가 일어나는 곳을 '자유 저장소 (free store; 또는 heap)' 라고 하며, '참조 (reference)' 는 '자유 저장소' 에 있는 할당된 메모리 영역을 '참조하는 (또는 가리키는; refer to)' 것에서 유래한 말입니다. 구조체나 열거체 같은 '값 타입 (value type)' 은 '자유 저장소' 가 아니라 '스택 (stack)' 이라는 '정적인 메모리' 공간에 생기기 것이라서 메모리 관리의 대상이 아닙니다. 보다 자세한 내용은 위키피디아의 'Memory management' 항목 중 [Dynamic memory allocation](https://en.wikipedia.org/wiki/Memory_management#DYNAMIC) 부분과 [Stack-based memory allocation](https://en.wikipedia.org/wiki/Stack-based_memory_allocation) 항목을 참고하기 바랍니다.

[^stored-constant-property]: 원문은 'stored constant property' 로 직역하면 '저장된 상수 속성' 이라고 해야겠지만, 스위프트의 '저장 속성' 중에서 '상수' 인 것이라는 의미를 살리기 위해 '상수 저장 속성' 이라고 약간 의역해서 옮겼습니다. 우리 말로는 '저장 상수 속성' 보다 '상수 저장 속성' 이라는 말이 좀 더 자연스럽다고 느꼈기 때문입니다.

[^multiple-references]: 여기서 '다중 참조 (multiple references)' 는 한 인스턴스를 여러 개의 변수에서 동시에 참조하고 있는 상태를 말합니다.

[^deinitializer]: 'deinitializer' 를 '정리자' 라고 옮기는 이유는 스위프트 언어에서 '소멸자' 라는 말은 어울리지 않기 때문입니다. 이에 대해서는 [Deinitialization (객체 정리)]({% post_url 2017-03-03-Deinitialization %}) 의 '[참고 자료]({% post_url 2017-03-03-Deinitialization %}#참고-자료)' 부분을 참고하기 바랍니다.

[^gabage-collection]: '쓰레기 수집' 은 'gabage collection' 을 직역한 말에 가까운데, 제가 지은 말이 아니고 실제로 사용하는 말인 것 같아서 그대로 옮깁니다. 이에 대한 더 자세한 내용은 위키피디아의 [Garbage collection (computer science)](https://en.wikipedia.org/wiki/Garbage_collection_(computer_science)) 항목과 [쓰레기 수집 (컴퓨터 과학)](https://ko.wikipedia.org/wiki/쓰레기_수집_(컴퓨터_과학) 항목을 참고하기 바랍니다.