---
layout: post
comments: true
title:  "Swift 5.2: Properties (속성)"
date:   2020-05-30 10:00:00 +0900
categories: Swift Language Grammar Property
---

> Apple 에서 공개한 [The Swift Programming Language (Swift 5.3)](https://docs.swift.org/swift-book/) 책의 [Properties](https://docs.swift.org/swift-book/LanguageGuide/Properties.html) 부분[^Properties]을 번역하고, 설명이 필요한 부분은 주석을 달아서 정리한 글입니다.
>
> 현재 번역이 진행 중인데, 2020-06-22 에 Swift 5.3 이 발표되어, 이미 번역된 부분과 남은 부분 모두 Swift 5.3 을 기준으로 옮기도록 합니다. 완료된 목록은 [Swift 5.3: Swift Programming Language (스위프트 프로그래밍 언어)]({% post_url 2017-02-28-The-Swift-Programming-Language %}) 에서 확인할 수 있으며, 일부는 Swift 5.2 기준일 수 있습니다.

## Properties (속성)

_속성 (properties)_ 은 특정한 클래스나, 구조체, 또는 열거체와 결합되어 있는 값입니다. '저장 속성 (stored properties)' 은 상수 값과 변수 값을 인스턴스 내에 저장하는 반면, '계산 속성 (computed properties)' 은 값을 (저장하지 않고) 계산합니다. '계산 속성' 은 클래스, 구조체, 그리고 열거체에서 제공합니다. '저장 속성' 은 클래스와 구조체에서만 제공합니다.

저장 속성과 계산 속성은 보통 특정한 타입의 인스턴스와 결합되어 있습니다. 하지만, 속성은 타입 그 자체에 결합되어 있을 수도 있습니다. 이러한 속성을 '타입 속성 (type properties)' 이라고 합니다.

이와 더불어, 속성 값이 바뀌는 것을 감시하기 위해 '속성 관찰자 (property observers)' 를 정의할 수 있으며, 이것을 써서 자신만의 사용자 정의 응답을 할 수도 있습니다. '속성 관찰자' 는 자신이 직접 정의한 '저장 속성' 에도 추가할 수 있고, 상위 클래스에서 상속 받은 하위 클래스의 속성에도 추가할 수 있습니다.

여러 속성에 걸쳐 '획득자 (getter)' 와 '설정자 (setter)' 의 코드를 재사용하기 위해 '속성 포장 (property wrapper)' 이란 것을 사용할 수도 있습니다.

### Stored Properties (저장 속성)

'저장 속성' 중, 가장 간단한 양식은, 특정 클래스나 구조체의 인스턴스에 저장되는 상수나 변수입니다. 저장 속성은 (`var` 키워드를 쓰는) _변수 저장 속성 (variable stored properties)_ 일 수도 있고 (`let` 키워드를 쓰는) _상수 저장 속성 (constant stored properties)_ 일 수도 있습니다.

저장 속성을 정의하면서 '기본 설정 값 (default value)' 을 제공할 수 있는데, 이는 [Default Property Values (기본 설정 속성 값)]({% post_url 2016-01-23-Initialization %}#default-property-values-기본-설정-속성-값) 에서 설명합니다. 초기화하는 동안에 저장 속성에 대한 초기 값을 설정하고 수정할 수도 있습니다. 이는 '상수 저장 속성' 에서도 가능한 것으로, [Assigning Constant Properties During Initialization (초기화하는 동안 상수 속성 할당하기)]({% post_url 2016-01-23-Initialization %}#assigning-constant-properties-during-initialization-초기화하는-동안-상수-속성-할당하기) 에서 설명합니다.

아래 예제는 `FixedLengthRange` 라는 구조체를 정의하여, 생성된 후에는 범위의 크기 (length) 를 바꿀 수 없는 정수 범위를 묘사합니다:

```swift
struct FixedLengthRange {
 var firstValue: Int
 let length: Int
}
var rangeOfThreeItems = FixedLengthRange(firstValue: 0, length: 3)
// 이 범위는 정수 값 0, 1, 2 를 나타냅니다.
rangeOfThreeItems.firstValue = 6
// 이 범위는 이제 정수 값 6, 7, 8 을 나타냅니다.
```

`FixedLengthRange` 인스턴스는 `firstValue` 라는 '변수 저장 속성' 과 `length` 라는 '상수 저장 속성' 을 가지고 있습니다. 위 예제에 있는, `length` 는 새 범위를 생성할 때 초기화되면 그 다음부터는 바꿀 수 없는데, 이는 '상수 속성' 이기 때문입니다.

#### Stored Properties of Constant Structure Instances (상수 구조체 인스턴스의 저장 속성)

구조체의 인스턴스를 생성한 다음 이를 상수에 할당하면, 그 인스턴스에 있는 속성은 수정할 수 없으며, 이는 '변수 속성' 으로 선언된 경우라도 마찬가지입니다:

```swift
let rangeOfFourItems = FixedLengthRange(firstValue : 0, length: 4)
// 이 범위는 정수 값 0, 1, 2, 3 을 나타냅니다.
rangeOfFourItems.firstValue = 6
// 이는 에러를 보고하는데, firstValue 가 변수 속성이라도 마찬가지입니다.
```

이는 `rangeOfFourItems` 을 (`let` 키워드를 써서) 상수로 선언했기 때문이며, `firstValue` 가 변수 속성일지라도, `firstValue` 속성을 바꿀 수 없습니다.

이렇게 동작하는 것은 구조체가 _값 타입 (value types)_ 이기 때문입니다. 값 타입의 인스턴스를 상수로 표시하면, 거기에 속한 모든 속성도 그렇게 됩니다.

이같은 방식은, _참조 타입 (reference types)_ 인, 클래스에는 적용되지 않습니다. 참조 타입의 인스턴스를 상수에 할당하는 경우, 그 인스턴스의 '변수 속성' 을 여전히 바꿀 수 있습니다.

#### Lazy Stored Properties (느긋한 저장 속성)

_느긋한 저장 속성 (lazy stored property)_ 은 맨 처음 사용하는 순간까지 초기 값이 계산되지 않는 속성을 말합니다. 선언 앞에 `lazy` 수정자를 붙여서 느긋한 (lazy) 저장 속성임을 지시합니다.

> 느긋한 속성은 반드시 항상 (`var` 키워드를 써서) 변수로 선언해야 하는데, 인스턴스 초기화가 완료된 후에도 초기 값을 가져오지 못할 수도 있기 때문입니다. 상수 속성은 초기화가 완료되기 _전에 (before)_ 반드시 항상 값을 가져야 하므로, 'lazy (느긋한)' 것으로 선언할 수 없습니다.

느긋한 (lazy) 속성이 속성의 초기 값이 외부 요인에 의존해서 이 값을 인스턴스 초기화가 완료된 후에도 알 수 없을 때 유용합니다. 느긋한 (lazy) 속성은 또 속성의 초기 값을 설정하는 것이 복잡하고 계산 비용이 비싸서 필요하지 않거나 또 필요하기 전까진 수행하지 않는 것이 바람직한 경우에도 유용합니다.

아래 예제는 복잡한 클래스에서의 불필요한 초기화를 피하기 위해 느긋한 (lazy) 저장 속성을 사용합니다. 이 예제에서 정의한 `DataImporter` 와 `DataManager` 라는 두 개의 클래스는, 둘 다 일부분만 나타낸 것입니다:

```swift
class DataImporter {
  /*
  DataImporter 는 외부 파일에서 자료를 불러오는 클래스 입니다.
  이 클래스를 초기화하는데는 적지 않은 시간이 걸린다고 가정합니다.
  */
  var filename = "data.txt"
  // DataImporter 클래스가 자료를 불러오는 기능을 제공하는 곳은 여기입니다.
}

class DataManager {
  lazy var importer = DataImporter()
  var data = [String]()
  // DataManager 클래스가 자료를 관리하는 기능을 제공하는 곳은 여기입니다.
}

let manager = DataManager()
manager.data.append("Some data")
manager.data.append("Some more data")
// importer 속성을 위한 DataImporter 인스턴스는 아직 생성되지 않았습니다.
```

`DataManager` 클래스는 `data` 라는 저장 속성을 가지는데, `String` 값을 담는 새로운, 빈 배열로 초기화합니다. 비록 나머지 기능을 나타내진 않았지만, `DataManager` 클래스의 목적은 이 `String` 배열 자료에 대한 접근 기능을 제공하고 이를 관리하는 것입니다.

`DataManager` 클래스의 기능 중 하나는 파일에서 자료를 가져오는 것입니다. 이 기능은 `DataImporter` 클래스에서 제공하는데, 이를 초기화하는 데는 적지 않은 시간이 걸린다고 가정합니다. 이렇게 가정한 것은 `DataImporter` 인스턴스를 초기화하려면 `DataImporter` 인스턴스가 파일을 열어서 내용을 읽은 다음 메모리로 옮겨야 할 것이기 때문입니다.

`DataManager` 인스턴스가 매번 파일에서 가져온 자료만 관리하는 건 아닐 것이므로, `DataManager` 를 생성할 때 새 `DataImporter` 인스턴스를 생성할 필요는 없습니다. 그 대신, `DataImporter` 인스턴스가 맨 처음 사용된다면 그 때 생성하는 것이 더 합리적입니다.

`importer` 속성을 위한 `DataImporter` 인스턴스는, `lazy` 수정자로 표시했기 때문에, `filename` 속성을 조회할 때와 같이, `importer` 속성을 맨 처음 접근할 때 생성됩니다:

```swift
print(manager.importer.filename)
// importer 속성을 위한 DataImporter 인스턴스는 이제 막 생성됐습니다.
// "data.txt" 를 출력합니다.
```

> `lazy` 수정자로 표시된 속성에 다중 쓰레드로 동시에 접근할 때 이 속성이 아직 초기화되지 않은 상태라면, 이 속성은 한 번만 초기화될 것이라고 보장할 수 없습니다.

#### Stored Properties and Instance Variables (저장 속성과 인스턴스 변수)[^instance-variables]

오브젝티브-C 언어에 대한 경험이 있다면, 클래스 인스턴스에서 값과 참조를 저장하는 데는 _두 가지 (two)_ 방법이 있다는 것을 알고 있을 것입니다. 속성 외에도, '인스턴스 변수 (instance variables)' 를 속성에 저장된 값의 '백업 저장소 (backing store)' 로 사용할 수 있습니다.

스위프트는 이러한 개념을 '단일한 속성 선언' 으로 통합했습니다. 스위프트의 '속성' 에는 연관되어 있는 '인스턴스 변수' 가 없으며, 속성의 '백업 저장소' 에 직접 접근할 일이 없습니다. 이런 접근법은 서로 다른 곳에서 값에 접근할 때의 혼동을 없애주고, 속성 선언을 단일하고 명확한 문장으로 할 수 있게 해 줍니다. 속성에 대한 모든-이름, 타입, 그리고 메모리 관리 방식을 포함한-정보들은 단일한 위치인 '타입 정의 부분' 에서 정의합니다.

### Computed Properties (계산 속성)

클래스, 구조체, 그리고 열거체는, '저장 속성' 외에도,  _계산 속성 (computed properties)_ 을 정의할 수 있는데, 이는 실제로는 값을 저장하지 않습니다. 대신에, 제공하는 '획득자 (getter)' 와 '선택적인 설정자 (optional setter)'[^optional-setter] 를 써서 간접적으로 다른 속성의 값을 가져오거나 설정합니다.

```swift
struct Point {
  var x = 0.0, y = 0.0
}
struct Size {
  var width = 0.0, height = 0.0
}
struct Rect {
  var origin = Point()
  var size = Size()
  var center: Point {
    get {
      let centerX = origin.x + (size.width / 2)
      let centerY = origin.y + (size.height / 2)
      return Point(x: centerX, y: centerY)
    }
    set(newCenter) {
      origin.x = newCenter.x - (size.width / 2)
      origin.y = newCenter.y - (size.height / 2)
    }
  }
}
var square = Rect(origin: Point(x: 0.0, y: 0.0), size: Size(width: 10.0, height: 10.0))
let initialSquareCenter = square.center
square.center = Point(x: 15.0, y: 15.0)
print("square.origin is now at (\(square.origin.x), \(square.origin.y))")
// "square.origin is now at (10.0, 10.0)" 를 출력합니다.
```

이 예제는 기하학적 도형 작업을 위해 세 가지 구조체를 정의합니다.

* `Point` 는 한 점에 대한 x, y 좌표를 은닉합니다.
* `Size` 는 `width` 와 `height` 를 은닉합니다.
* `Rect` 는 '원점 (origin point)' 과 '크기 (size)' 를 사용하여 사각형을 정의합니다.

`Rect` 구조체는 `center` 라는 '계산 속성' 도 제공합니다. `Rect` 의 현재 중심 위치는 `origin` 과 `size` 로부터 항상 결정할 수 있으므로, 중심 점을 `Point` 값으로 저장할 필요가 없습니다. 대신, `Rect` 는 `center` 라는 '계산 변수' 에 대한 사용자 정의 '획득자 (getter)' 와 '설정자 (setter)' 를 정의하여, 사각형의 `center` 를 마치 실제 저장 속성인 것처럼 사용할 수 있게 해줍니다.

위의 예제에서는 `square` 라는 새로운 `Rect` 변수를 생성합니다. `square` 변수의 원점은 `(0, 0)`, 폭과 높이는 `10` 으로 초기화 됩니다. 이 '정사각형 (square)' 은 아래 그림에서 파란색 사각형으로 표현됩니다.

`square` 변수의 `center` 속성은 이제 '점 구문 표현 (`square.center`)' 을 통해 접근하게 되는데, 이는 `center` 의 '획득자 (getter)' 를 호출하여, 현재 속성의 값을 가져옵니다. 이 '획득자 (getter)' 는, 존재하고 있는 값을 반환하는게 아니고, 정사각형의 중심을 나타내는 새 `Point` 를 실제로 계산한 다음 반환합니다. 위에서 봤듯이, '획득자' 는 중심점이 `(5, 5)` 라고 정확하게 반환합니다.

`center` 속성은 이제 새 값인 `(15, 15)` 로 설정되며, 이는 정사각형을 오른쪽 위로 이동하게 되는데, 이 새 위치는 아래 그림의 오렌지 정사각형으로 나타냈습니다. `center` 속성을 설정하면 `center` 의 '설정자 (setter)' 를 호출하며, 이는 `origin` 저장 속성의 `x` 와 `y` 값을 수정하여, 정사각형을 새 위치로 이동합니다.

![computed properties](/assets/Swift/Swift-Programming-Language/Properties-computed-property.png)

#### Shorthand Setter Declaration (설정자 선언의 약칭 표현)

계산 속성의 '설정자 (setter)' 가 설정할 새 값에 대한 이름을 정의하지 않을 경우, 기본 제공되는 이름인 `newValue` 를 사용합니다. 다음은 이 '약칭 표현법 (shorthand notation)' 의 이점을 활용하여 `Rect` 구조체를 다른 방법으로 만들어 본 것입니다:

```swift
struct AlternativeRect {
  var origin = Point()
  var size = Size()
  var center: Point {
    get {
      let centerX = origin.x + (size.width / 2)
      let centerY = origin.y + (size.height / 2)
      return Point(x: centerX, y: centerY)
    }
    set {
      origin.x = newValue.x - (size.width / 2)
      origin.y = newValue.y - (size.height / 2)
    }
  }
}
```

#### Shorthand Getter Declaration (획득자 선언의 약칭 표현)

'획득자 (getter)' 의 전체 본문이 '단일 표현식 (single expression)' 으로 되어 있는 경우, '획득자' 는 암시적으로 그 표현식을 반환합니다. 다음은 이 '약칭 표현법' 과 '설정자' 에 대한 '약칭 표현법' 이점을 활용하여 `Rect` 구조체를 다른 방법으로 만들어 본 것입니다:

```swift
struct CompactRect {
  var origin = Point()
  var size = Size()
  var center: Point {
    get {
      Point(x: origin.x + (size.width / 2), y: origin.y + (size.height / 2))
    }
    set {
      origin.x = newValue.x - (size.width / 2)
      origin.y = newValue.y - (size.height / 2)
    }
  }
}
```

'획득자' 에서 `return` 을 생략하는 것은 함수에서 `return` 을 생략하는 것과 같은 규칙을 따르는 것으로, 이는 [Functions With an Implicit Return (암시적으로 반환하는 함수)]({% post_url 2020-06-02-Functions %}#functions-with-an-implicit-return-암시적으로-반환하는-함수) 에서 설명했었습니다.

#### Read-Only Computed Properties (읽기-전용 계산 속성)

획득자는 있지만 설정자는 없는 계산 속성을 _읽기-전용 계산 속성 (read-only computed property)_ 라고 합니다. '읽기-전용 계산 속성' 은 항상 값을 반환하고, '점 구문 표현' 으로 접근할 수 있지만, 다른 값을 설정할 수는 없습니다.

> 계산 속성은-읽기 전용 계산 속성도 포함하여- 반드시 `var` 키워드를 써서 변수 속성으로 선언해야 하는데, 이는 값이 고정된 것이 아니기 때문입니다. `let` 키워드는 상수 속성에만 사용하며, 인스턴스 초기화에서 한 번 설정한 값은 바꿀 수 없음을 지시하는 것입니다.

읽기-전용 계산 속성의 선언은 `get` 키워드와 중괄호를 제거하여 간소화 할 수 있습니다:

```swift
struct Cuboid {
  var width = 0.0, height = 0.0, depth = 0.0
  var volume: Double {
    return width * height * depth
  }
}
let fourByFiveByTwo = Cuboid(width: 4.0, height: 5.0, depth: 2.0)
print("the volume of fourByFiveByTwo is \(fourByFiveByTwo.volume)")
// "the volume of fourByFiveByTwo is 40.0" 를 출력합니다.
```

이 예제는 `Cuboid` 라는 새 구조체를 정의하여, `width`, `height`, 그리고 `depth` 속성을 가진 3D 직사각형 상자를 표현합니다. 이 구조체는 `volume` 이라는 읽기-전용 계산 속성도 가지고 있는데, 이는 '직육면체 (cuboid)'[^cuboid] 의 현재 부피를 계산하고 반환합니다. `volume` 은 '설정 가능하다는 (settable)' 것은 말이 안되는데, 왜냐면 특정한 `volume` 값에 대한 `width`, `height`, 그리고 `depth` 값이 어떤 것인지 모호하기 때문입니다. 그렇지만, `Cuboid` 가 읽기-전용 계산 속성을 제공하여 현재 계산된 부피를 외부 사용자도 알 수 있게 하는 것은 유용합니다.

### Property Observers (속성 관찰자)

'속성 관찰자 (property observers)' 는 속성 값이 바뀌는 것을 관찰하여 이에 대한 응답을 합니다. 속성 관찰자는 속성의 값이 설정될 때마다 호출되는데, 심지어 새 값이 현재 속성의 값과 같은 경우에도 호출됩니다.

직접 정의한 저장 속성이라면 어떤 것에든, '지연 저장 속성' 만 아니라면, 속성 관찰자를 추가할 수 있습니다. 상속받은 속성에도 (저장 속성이든 계산 속성이든 상관없이) 어느 것에든 속성 관찰자를 추가할 수 있으며, 하위 클래스에서 그 속성을 '재정의 (overriding)' 하면 됩니다. '재정의 하지 않은 계산 속성 (nonoverridden computed properties)'[^nonoverridden-computed-properties] 에는 속성 관찰자를 정의 할 필요가 없는데, 이는 계산 속성의 설정자 (setter) 에서 해당 값의 변화를 관찰하고 응답할 수 있기 때문입니다. '속성 재정의 (property overriding)' 는 [Overriding (재정의하기)]({% post_url 2020-03-31-Inheritance %}#overriding-재정의하기) 에서 설명합니다.

속성에 다음 관찰자 중에서 하나만 정의할 지 둘 다 정의할 지 선택할 수 있습니다:

* `willSet` 은 값이 저장되기 바로 직전에 호출됩니다.
* `didSet` 은 새 값이 저장된 바로 직후에 호출됩니다.

`willSet` 관찰자를 구현하면, 새 속성 값은 상수 매개 변수의 형태로 전달됩니다. 이 매개 변수의 이름은 `willSet` 구현부에서 지정할 수 있습니다. 구현부에서 매개 변수 이름과 괄호를 쓰지 않으면, 이 매개 변수는 기본 제공되는 매개 변수 이름인 `newValue` 를 사용하게 됩니다.

마찬가지로, `didSet` 관찰자를 구현하면, 이전 속성 값을 담고 있는 상수 매개 변수가 전달됩니다. 매개 변수에 이름을 지정할 수도 있고 기본 제공 매개 변수 이름인 `oldValue` 를 사용할 수도 있습니다. 자신이 가지고 있는 `didSet` 관찰자에서 속성의 값을 할당하면, 새로 할당한 값이 방금 설정된 값을 대체하게 됩니다.

> 상위 클래스 속성의 `willSet` 과 `didSet` 관찰자는, 하위 클래스의 초기자에서 속성을 설정할 때, 상위 클래스의 초기자를 호출한 후, 호출됩니다. 이들은 클래스 속성을 설정하는 동안, 상위 클래스의 초기자가 호출되기 전에는, 호출되지 않습니다.
>
> '초기자 위임 (initializer delegation)' 에 대한 더 많은 정보는, [Initializer Delegation for Value Types (값 타입을 위한 초기자의 위임)]({% post_url 2016-01-23-Initialization %}#initializer-delegation-for-value-types-값-타입을-위한-초기자의-위임) 과 [Initializer Delegation for Class Types (클래스 타입을 위한 초기자의 위임)]({% post_url 2016-01-23-Initialization %}#initializer-delegation-for-class-types-클래스-타입을-위한-초기자의-위임) 을 참고하기 바랍니다.

다음은 `willSet` 과 `didSet` 의 실제 사례입니다. 아래 예제는 `StepCounter` 라는 새로운 클래스를 정의하여, 한 사람이 걷는 동안의 총 걸음 수를 추적합니다. 이 클래스는 사람의 운동 과정을 매일 매일 추적하기 위해 '만보계 (pedometer)' 나 다른 '걸음 카운터 (step counter)' 의 입력 데이터를 같이 사용할 수도 있을 것입니다.

```swift
class StepCounter {
  var totalSteps: Int = 0 {
    willSet(newTotalSteps) {
      print("About to set totalSteps to \(newTotalSteps)")
    }
    didSet {
      if totalSteps > oldValue  {
        print("Added \(totalSteps - oldValue) steps")
      }
    }
  }
}
let stepCounter = StepCounter()
stepCounter.totalSteps = 200
// About to set totalSteps to 200
// Added 200 steps
stepCounter.totalSteps = 360
// About to set totalSteps to 360
// Added 160 steps
stepCounter.totalSteps = 896
// About to set totalSteps to 896
// Added 536 steps
```

`StepCounter` 클래스는 타입이 `Int` 인 `totalSteps` 속성을 선언합니다. 이것은 `willSet` 과 `didSet` 관찰자를 가지고 있는 저장 속성입니다.

`totalSteps` 의 `willSet` 과 `didSet` 관찰자는 속성에 새 값이 할당될 때마다 호출됩니다. 심지어 새 값이 현재 값과 같은 경우에도 그렇습니다.

이 예제의 `willSet` 관찰자는 새로 지정할 값에 `newTotalSteps` 라는 사용자 정의 매개 변수 이름을 사용합니다. 이 예제에서는, 이는 단순히 설정하려는 값을 출력하기만 합니다.

`didSet` 관찰자는 `totalSteps` 값이 갱신된 후에 호출됩니다. 이는 `totalSteps` 의 새 값을 이전 값과 비교합니다. 총 걸음 수가 증가다면, 얼마나 더 걸었는지 나타내기 위해 메시지를 출력합니다. `didSet` 관찰자는 이전 값에 대해 사용자 정의 매개 변수 이름을 제공하지 않고, 그 대신 기본 제공되는 이름인 `oldValue` 를 사용합니다.

> 관찰자를 가지고 있는 속성을 함수의 '입-출력 매개 변수 (in-out parameter)' 로 전달하면, `willSet` 과 `didSet` 관찰자는 항상 호출됩니다. 이는 '입-출력 매개 변수' 의 '복사해 들어가고 (copy-in)' '복사해 나오는 (copy-out)' 모델 때문입니다: 함수의 끝에서 속성의 값은 항상 다시 쓰여집니다. '입-출력 매개 변수' 의 이러한 동작 방식에 대한 더 자세한 논의는, [In-Out Parameters (입-출력 매개 변수)](https://docs.swift.org/swift-book/ReferenceManual/Declarations.html#ID545) 를 참고하기 바랍니다.

### Property Wrappers (속성 포장)

'속성 포장 (property wrapper)' 은 속성 저장 방법을 관리하는 코드와 속성을 정의하는 코드를 구분하는 '계층 (layer)' 을 추가합니다. 예를 들어, 쓰레드-안전성 검사를 제공하는 속성이나 실제 자료를 DB 에 저장하는 속성이 있다면, 이에 해당하는 코드를 모든 속성마다 작성해야 합니다. 이 때 속성 포장을 사용하여, 포장을 정의할 때 관리 코드를 한 번만 작성하면, 그 관리 코드를 여러 속성에 적용하여 재사용하면 됩니다.[^property-wrapper]

속성 포장을 정의하려면, 구조체, 열거체, 또는 클래스를 만들 때 `wrappedValue` 속성을 정의하면 됩니다. 아래 코드에서, `TwelveOrLess` 구조체는 포장 값이 항상 12 보다 작거나 같도록 보장합니다. 더 큰 수를 저장하라고 요청하면, 그 대신 12 를 저장합니다.

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number: Int
    init() { self.number = 0 }
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}
```

'설정자 (setter)' 는 새 값이 12 보다 작음을 보장하고, '획득자 (getter)' 는 저장된 값을 반환합니다.

> 위의 예제에서 `number` 선언은 `private` 변수라고 표시됐는데, 이는 `number` 가 `TwelveOrLess` 구현에서만 사용됨을 보장합니다. 다른 곳에서 작성된 코드는 `wrappedValue` 의 '획득자 (getter)' 와 '설정자 (setter)' 로 값에 접근해야 하며, `number` 를 직접 사용할 수 없습니다. `private` 에 대한 정보는, [Access Control (접근 제어)]({% post_url 2020-04-28-Access-Control %}) 를 참고하기 바랍니다.

'속성 (property)' 에 '포장 (wrapper)' 을 적용하려면 속성 앞에 '특성 (attribute)' 처럼 포장의 이름을 작성하면 됩니다. 다음은 작은 직사각형을 저장하는 구조체로, `TwelveOrLess` '속성 포장 (property wrapper)' 에서 구현한 것과 같은 (다소 임의의) "작은 (small)" 에 대한 정의를 사용합니다:

```swift
struct SmallRectangle {
  @TwelveOrLess var height: Int
  @TwelveOrLess var width: Int
}

var rectangle = SmallRectangle()
print(rectangle.height)
// "0" 을 출력합니다.

rectangle.height = 10
print(rectangle.height)
// "10" 을 출력합니다.

rectangle.height = 24
print(rectangle.height)
// "12" 를 출력합니다.
```

`height` 와 `width` 속성은 `TwelveOrLess` 정의에서 초기 값을 가져오는데, 이는 `TwelveOrLess.number` 를 0 으로 설정합니다. 10 을 `rectangle.height` 에 저장하는 것은 성공하는데 작은 수이기 때문입니다. 24 를 저장하려고 하면 실제로는 12 값이 대신 저장되는데, 24 는 속성 '설정자 (setter)' 의 규칙에 따르면 너무 크기 때문입니다.

속성에 '포장 (wrapper)' 을 적용하면, 포장을 위해 저장소를 제공하는 코드와 포장을 통해 속성에 대한 접근을 제공하는 코드를 컴파일러가 만들어서 통합해 줍니다. ('속성 포장 (property wrapper)' 은 포장된 값을 저장하는 책임을 지므로, 따로 만들어져서 통합되는 코드가 없습니다.) '특성 구문 표현 (attribute syntax)' 의 이점을 특별히 사용하지 않고, '속성 포장' 역할을 하는 코드를 직접 작성할 수도 있습니다. 예를 들어, 다음은 이전 코드에 있는 `SmallRectangle` 에서, `@TwelveOrLess` 특성 대신, `TwelveOrLess` 구조체를 명시적으로 써서 속성을 포장한 버전입니다:

```swift
struct SmallRectangle {
  private var _height = TwelveOrLess()
  private var _width = TwelveOrLess()
  var height: Int {
    get { return _height.wrappedValue }
    set { _height.wrappedValue = newValue }
  }
  var width: Int {
    get { return _width.wrappedValue }
    set { _width.wrappedValue = newValue }
  }
}
```

`_height` 와 `_width` 속성은 '속성 포장' 인, `TwelveOrLess` 의 인스턴스를 저장합니다. `height` 와 `width` 의 획득자 (getter) 및 설정자 (setter) 는 `wrappedValue` 속성에 대한 접근을 포장합니다.

#### Setting Initial Values for Wrapped Properties (포장된 속성에 대한 초기 값 설정하기)

위 예제의 코드는 '포장된 속성 (wrapped property)' 의 초기 값을 설정할 때 `TwelveOrLess` 정의에 있는 `number` 초기 값을 부여합니다. 이 '속성 포장 (property wrapper)' 을 사용하는 코드는, `TwelveOrLess` 로 포장된 속성에 대해 다른 초기 값을 지정할 수 없습니다-예를 들어, `SmallRectangle` 의 정의에서 `height` 나 `width` 에 초기 값을 부여할 수 없습니다. 초기 값 설정이나 다른 사용자 동작을 지원하고 싶으면, '속성 포장' 에 초기자를 추가할 필요가 있습니다. 다음은 `TwelveOrless` 를 확장한 버전인 `SmallNumber` 인데 여기에는 포장된 값과 최대 값을 설정하는 초기자가 정의되어 있습니다:

```swift
@propertyWrapper
struct SmallNumber {
  private var maximum: Int
  private var number: Int

  var wrappedValue: Int {
    get { return number }
    set { number = min(newValue, maximum) }
  }
  init() {
    maximum = 12
    number = 0
  }
  init(wrappedValue: Int) {
    maximum = 12
    number = min(wrappedValue, maximum)
  }
  init(wrappedValue: Int, maximum: Int) {
    self.maximum = maximum
    number = min(wrappedValue, maximum)
  }
}
```

`SmallNumber` 의 정의는 세 개의 초기자를 포함하고 있으며-`init()`, `init(wrappedValue:)`, 그리고 `init(wrappedValue:maximum:)`-이는 아래의 예제에서 포장된 값과 최대 값을 설정할 때 사용합니다. '초기자' 와 '초기자 구문 표현 (initializer syntax)' 에 대한 정보는 [Initialization (초기화)]({% post_url 2016-01-23-Initialization %}) 를 참고하기 바랍니다.

속성에 포장을 적용하면서 초기 값을 지정하지 않을 경우, 스위프트는 `init()` 초기자를 사용하여 포장을 설정합니다. 예를 들면 다음과 같습니다:

```swift
struct ZeroRectangle {
  @SmallNumber var height: Int
  @SmallNumber var width: Int
}

var zeroRectangle = ZeroRectangle()
print(zeroRectangle.height, zeroRectangle.width)
// "0 0" 를 출력합니다.
```

`height` 와 `width` 를 포장하는 `SmallNumber` 인스턴스는 `SmallNumber()` 를 호출하여 생성됩니다. 이 초기자 내부에 있는 코드는 초기 포장 값과 초기 최대 값을, 기본 설정 값인 0 과 12 를 사용하여, 설정합니다. 속성 포장은, `SmallRectangle` 에서 `TwelveOrLess` 를 사용한 이전 예제와 같이, 여전히 초기 값을 모두 제공합니다. 그 예제와 다른 점은, `SmallNumber` 는 속성의 선언 부에서 초기 값을 작성하는 기능도 지원한다는 것입니다.

속성에 대한 초기 값을 지정하게 되면, 스위프트는 `init(wrappedValue:)` 초기자를 사용하여 포장을 설정합니다. 예를 들면 다음과 같습니다:

```swift
struct UnitRectangle {
  @SmallNumber var height: Int = 1
  @SmallNumber var width: Int = 1
}

var unitRectangle = UnitRectangle()
print(unitRectangle.height, unitRectangle.width)
// "1 1" 를 출력합니다.
```

포장을 가진 속성에 `= 1` 를 작성하면, 이는 `init(wrappedValue:)` 초기자에 대한 호출로 번역됩니다. `height` 와 `width` 를 포장하는 `SmallNumber` 인스턴스는 `SmallNumber(wrappedValue: 1)` 을 호출하여 생성됩니다. 초기자는 포장 값으로 여기서 지정한 값을 사용하며, 최대 값은 기본 설정 값인 12 를 사용합니다.

'사용자 정의 특성 (custom attribute)' 뒤의 괄호 안에 인자를 작성하면, 스위프트는 이 인자를 받는 초기자를 사용하여 포장을 설정합니다. 예를 들어, 초기 값과 최대 값을 제공할 경우, 스위프트는 `init(wrappedValue:maximum:)` 초기자를 사용합니다:

```swift
struct NarrowRectangle {
  @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
  @SmallNumber(wrappedValue: 3, maximum: 4) var width: Int
}

var narrowRectangle = NarrowRectangle()
print(narrowRectangle.height, narrowRectangle.width)
// "2 3" 를 출력합니다.

narrowRectangle.height = 100
narrowRectangle.width = 100
print(narrowRectangle.height, narrowRectangle.width)
// "5 4" 를 출력합니다.
```

`height` 를 포장하는 `SmallNumber` 의 인스턴스는 `SmallNumber(wrappedValue: 2, maximum: 5)` 를 호출하여 생성되고, `width` 를 포장하는 인스턴스는 `SmallNumber(wrappedValue: 3, maximum: 4)` 를 호출하여 생성됩니다.

'속성 포장 (property wrapper)' 에 인자를 포함하는 것으로써, 포장의 초기 상태를 설정할 수도 있고 포장을 생성할 때 다른 옵션들을 전달할 수도 있게 됩니다. 이런 구문 표현은 속성 포장을 사용하는 가장 일반적인 방법입니다. '특성 (attributes)' 에 필요한 인자라면 뭐든지 제공할 수 있고, 그러면 이들은 초기자로 전달됩니다.

속성 포장 인자를 포함할 때, '할당 (연산자)' 를 사용하여 초기 값을 지정할 수도 있습니다. 스위프트는 '할당 (assignment)' 을 `wrappedValue` 인자처럼 취급하여 그 인자까지 받아 들이는 초기자를 사용하게 됩니다. 예를 들면 다음과 같습니다:

```swift
struct MixedRectangle {
  @SmallNumber var height: Int = 1
  @SmallNumber(maximum: 9) var width: Int = 2
}

var mixedRectangle = MixedRectangle()
print(mixedRectangle.height)
// "1" 를 출력합니다.

mixedRectangle.height = 20
print(mixedRectangle.height)
// "12" 를 출력합니다.
```

`height` 를 포장하는 `SmallNumber` 인스턴스는 `SmallNumber(wrappedValue : 1)` 를 호출하여 생성되며, 이는 기본 설정 최대 값으로 12 를 사용합니다. `width` 를 포장하는 인스턴스는 `SmallNumber(wrappedValue: 2, maximum: 9)` 를 호출하여 생성됩니다.

#### Projecting a Value From a Property Wrapper (속성 포장에 있는 값 드러내기)

'포장된 값 (wrapped value)' 외에도, 속성 포장은 '_드러낸 값 (projected value)_' 를 정의하여 추가적인 기능을 내보일 수있습니다-예를 들어, DB 에 대한 접근을 관리하는 속성 포장은 '드러낸 값' 을 써서 `flushDatabaseConnection()` 메소드를 내보일 수 있습니다. '드러낸 값' 의 이름은, 달러 기호 (`$`) 로 시작 한다는 점만 빼면, '포장된 값' 과 같습니다. `$` 로 시작하는 속성을 코드에서 직접 정의할 수는 없기 때문에 '드러낸 값' 이 직접 정의한 속성을 방해가 될 일은 절대 없습니다.

위의 `SmallNumber` 예제에서, 속성에 너무 큰 수를 설정하게 되면, 속성 포장이 그 수를 저장하기 전에 먼저 조정하게 됩니다. 아래 코드는 `SmallNumber` 구조체에 `projectedValue` 속성을 추가하여 속성 포장이 새 값을 저장하기 전에 먼저 조정했는지 여부를 추적합니다.

```swift
@propertyWrapper
struct SmallNumber {
  private var number: Int
  var projectedValue: Bool
  init() {
    self.number = 0
    self.projectedValue = false
  }
  var wrappedValue: Int {
    get { return number }
    set {
      if newValue > 12 {
        number = 12
        projectedValue = true
      } else {
        number = newValue
        projectedValue = false
      }
    }
  }
}
struct SomeStructure {
  @SmallNumber var someNumber: Int
}
var someStructure = SomeStructure()

someStructure.someNumber = 4
print(someStructure.$someNumber)
// "false" 를 출력합니다.

someStructure.someNumber = 55
print(someStructure.$someNumber)
// "true" 를 출력합니다.
```

`someStructure.$someNumber` 를 작성하여 포장의 '드러낸 값' 에 접근합니다. 4 처럼 작은 수를 저장한 후엔, `someStructure.$someNumber` 의 값은 `false` 가 됩니다. 하지만, 55 같이, 너무 큰 수를 저장하려고 하면 '드러낸 값' 은 `true` 가 됩니다.

속성 포장은 '드러낸 값' 으로 어떤 타입의 값이라도 반환할 수 있습니다. 이 예제에서는, 속성 포장이 단 하나의 정보-그 수를 조정했는지의 여부-만을 내보이므로 '드러낸 값' 을 '불리언 값 (Boolean value)' 으로 내보입니다. 더 많은 정보를 내보여야 하는 포장은 어떤 다른 자료 타입의 인스턴스를 반환할 수도 있고, 아니면 `self` 를 반환하여 포장의 인스턴스 자체를 '드러낸 값' 으로 내보일 수도 있습니다.

'드러낸 값' 을, 속성 '획득자 (getter)' 나 인스턴스 메소드 같이, 타입에 있는 코드에서 접근할 때는, 속성 이름 앞의 `self.` 를 생략하여, 다른 속성에 접근하는 것처럼, 접근할 수 있습니다. 다음 예제에 있는 코드는 `height` 와 `width` 에 대한 포장의 '드러낸 값' 을 `$height` 와 `$width` 로 참조합니다.

```swift
enum Size {
  case small, large
}

struct SizedRectangle {
  @SmallNumber var height: Int
  @SmallNumber var width: Int

  mutating func resize(to size: Size) -> Bool {
    switch size {
    case .small:
      height = 10
      width = 20
    case .large:
      height = 100
      width = 100
    }
    return $height || $width
  }
}
```

'속성 포장 구문 표현 (property wrapper syntax)' 은 단지 '획득자 (getter)' 와 '설정자 (setter)' 를 가지고 있는 속성의 '구문 표현을 쉽게 나타낸 것 (syntactic sugar)' 에 불과하기 때문에, `height` 와 `width` 에 접근하는 동작은 다른 어떠한 속성에든 접근하는 것과 같은 것입니다. 예를 들어, `resize(to:)` 에 있는 코드는 '속성 포장' 을 사용하여 `height` 와 `width` 에 접근합니다. `resize(to: .large)` 를 호출하는 경우면, 'switch 문' 의 `.large` 'case 절' 에 해당하는 코드가 직사각형의 높이와 폭을 100 으로 설정합니다. 포장은 해당 속성의 값이 12 보다 커지는 것을 막은 다음, '드러낸 값' 을 `true` 로 설정하여, 값이 조정되었다는 사실을 기록합니다. `resize(to:)` 의 끝에서, 반환 구문이 `$heigh` 와 `$width` 를 검사하여 속성 포장이 `height` 나 `width` 를 조정했는 지를 확인합니다.

### Global and Local Variables (전역 변수 및 지역 변수)

위에서 설명한 속성 계산과 속성 관찰 같은 기능들은 _전역 변수 (global variables)_ 와 _지역 변수 (local variables)_ 에서도 사용할 수 있습니다. 전역 변수란 함수든, 메소드든, 클로저든, 또는 타입이든 어떠한 영역 외부에서 정의된 변수를 말합니다. 지역 변수는 함수, 메소드, 또는 클로저 영역 내에서 정의된 변수를 말합니다.

이전 장에서 접했던 전역 변수 및 지역 변수는 모두 _저장 변수 (stored variables)_ 였습니다. '저장 변수' 는, '저장 속성' 과 마찬가지로, 정해진 타입의 값에 대한 저장 공간을 제공해서 값을 설정하고 가져올 수 있습니다.

하지만, 전역이든 지역이든 상관없이, _계산 변수 (computed variables)_ 를 정의할 수도 있고 저장 변수에 대한 '관찰자 (observers)' 를 정의할 수도 있습니다. '계산 변수' 는, 값을 저장하든 대신, 직접 계산하며 '계산 속성' 과 같은 방식으로 작성하면 됩니다.

> 전역 상수와 전역 변수는, [Lazy Stored Properties (느긋한 저장 속성)](#lazy-stored-properties-느긋한-저장-속성) 과 같은 태도로, 항상 '느긋하게 (lazily)' 계산됩니다.
'느긋한 저장 속성' 과 다른 점은, 전역 상수와 전역 변수는 `lazy` 수정자로 표시할 필요가 없다는 것입니다.
>
> 지역 상수와 지역 변수는 절대로 '느긋하게 (lazily)' 계산되지 않습니다.

### Type Properties (타입 속성)

인스턴스 속성은 특정한 타입의 인스턴스에 속해 있는 속성입니다. 그 타입의 새로운 인스턴스를 생성할 때마다, 다른 어떤 인스턴스와는 구분되는, 자신만의 속성 값 집합을 가지게 됩니다.

해당 타입의 어떤 한 인스턴스가 아니라, 타입 그 자체에 속하는 속성을 정의할 수도 있습니다. 이 속성의 복사본은, 해당 타입의 인스턴스를 생성한 개수와는 상관없이, 단 하나만 존재하게 됩니다. 이런 종류의 속성을 _타입 속성 (type properties)_ 라고 합니다.

타입 속성은 특정한 타입의 _모든 (all)_ 인스턴스에 보편적인 값을 정의하는데 유용하며, 가령 (C 언어의 static (정적) 상수 같이) 모든 인스턴스가 사용할 수 있는 상수나, (C 언어의 static (정적) 변수 같이) 해당 타입의 모든 인스턴스에 전역인 값을 저장하는 변수 속성 등이 이에 해당합니다.

'저장 타입 속성 (stored type properties)' 은 변수이거나 상수일 수 있습니다. '계산 타입 속성 (computed type properties)' 은 항상 '변수 속성' 인 것으로 선언해야 하며, , '계산 인스턴스 속성' 과 그 방식이 같습니다.

> '저장 인스턴스 속성' 과 달리, '저장 타입 속성' 은 반드시 항상 '기본 설정 값' 을 가지고 있어야 합니다. 이것은 타입 그 자체는 초기화시에 '저장 타입 속성' 에 값을 할당할 수 있는 초기자를 가지고 있지 않기 때문입니다.
>
> '저장 타입 속성' 은 '느긋하게 (lazily)' 처음 접근할 때 초기화됩니다. 이는 여러 쓰레드에서 동시에 접근해도, 단 한 번만 초기화됨을 보장하며, `lazy` 수정자로 표시할 필요도 없습니다.

#### Type Property Syntax (타입 속성 구문 표현)

C 언어 및 오브젝티브-C 언어에서는, 타입과 관련된 정적 상수와 정적 변수를 '_전역 (global)_ 정적 변수' 의 형태로 정의합니다. 반면, 스위프트의, 타입 속성은 타입 외곽 중괄호 안에 있는 타입 정의 부분에서 작성하며, 각 타입 속성은 명시적으로 자신이 지원하는 타입으로 영역의 범위가 정해집니다.

타입 속성의 정의는 `static` 키워드를 사용합니다. 클래스 타입의 '계산 타입 속성' 에는, `class` 키워드를 대신 써서 하위 클래스에서 상위 클래스의 구현을 '재정의 (override)' 할 수 있게 허용할 수 있습니다. 아래 예제는 저장 타입 속성 및 계산 타입 속성에 대한 '구문 표현 (syntax)' 을 보여줍니다:

```swift
struct SomeStructure {
  static var storedTypeProperty = "Some value."
  static var computedTypeProperty: Int {
    return 1
  }
}
enum SomeEnumeration {
  static var storedTypeProperty = "Some value."
  static var computedTypeProperty: Int {
    return 6
  }
}
class SomeClass {
  static var storedTypeProperty = "Some value."
  static var computedTypeProperty: Int {
    return 27
  }
  class var overrideableComputedTypeProperty: Int {
    return 107
  }
}
```

> 위에 있는 '계산 타입 속성' 예제는 읽기-전용 계산 타입 속성에 대한 것이지만, '계산 인스턴스 속성' 과 같은 구문 표현을 사용하여 '읽고-쓰기 계산 타입 속성 (read-write computed type properties)' 도 정의할 수 있습니다.

#### Querying and Setting Type Properties (타입 속성 조회하고 설정하기)

타입 속성은, 인스턴스 속성과 마찬가지로, '점 구문 표현 (dot syntax)' 를 사용하여 조회하고 설정할 수 있습니다. 하지만, 타입 속성의 조회와 설정은 _타입 (type)_ 에서 하는 것이지, 그 타입의 인스턴스에서 하는 것이 아닙니다. 예를 들면 다음과 같습니다:

```swift
print(SomeStructure.storedTypeProperty)
// "Some value." 를 출력합니다.
SomeStructure.storedTypeProperty = "Another value."
print(SomeStructure.storedTypeProperty)
// "Another value." 를 출력합니다.
print(SomeEnumeration.computedTypeProperty)
// "6" 를 출력합니다.
print(SomeClass.computedTypeProperty)
// "27" 를 출력합니다.
```

다음은 다중 음향 채널용 음향 측정 기기를 모델링하는 구조체에서 두 개의 저장 타입 속성을 사용하는 예제입니다. 각 채널은 `0` 에서 `10` 사이의 정수 값의 음향 단계를 가지고 있습니다.

아래 그림은 스테레오 음향 측정 기기를 모델링하기 위해 이 두 음향 채널을 결합하는 방법을 묘사하고 있습니다. 채널의 음향 단계가 `0` 이면, 그 채널의 모든 빛은 꺼집니다. 이 그림에서, 왼쪽 채널은 현재 단계가 `9` 이고, 오른쪽 채널은 현재 단계가 `7` 입니다:

![audio level meter](/assets/Swift/Swift-Programming-Language/Properties-audio-level-meter.jpg)

위에서 묘사한 음향 채널은 `AudioChannel` 구조체의 인스턴스로써 표현됩니다:

```swift
struct AudioChannel {
  static let thresholdLevel = 10
  static var maxInputLevelForAllChannels = 0
  var currentLevel: Int = 0 {
    didSet {
      if currentLevel > AudioChannel.thresholdLevel {
        // 새 음향 단계를 임계 값까지로 쳐냅니다.
        currentLevel = AudioChannel.thresholdLevel
      }
      if currentLevel > AudioChannel.maxInputLevelForAllChannels {
        // 지금까지 중에서 이것이 새로운 최대 입력 단계인 것으로 저장합니다.
        AudioChannel.maxInputLevelForAllChannels = currentLevel
      }
    }
  }
}
```

`AudioChannel` 구조체는 기능을 지원하는 용도로 두 개의 '저장 타입 속성' 을 정의합니다. 첫 번째인, `thresholdLevel` 은, 음향 단계가 취할 수 있는 최대 임계 값을 정의합니다. 이는 모든 `AudioChannel` 인스턴스에 대해서 상수 값 `10` 입니다. `10` 보다 높은 값의 음향 신호가 들어 오면, 이 임계 값까지로 쳐냅니다. (이는 아래에서 설명합니다.)

두 번째 타입 속성은 `maxInputLevelForAllChannels` 라는 '변수 저장 속성' 입니다. 이는 `AudioChannel` 인스턴스 중에서 _어떤 (any)_ 것이든 지금까지 수신한 최대 입력 값을 추적합니다. 초기 값은 `0` 에서 시작합니다.

`AudioChannel` 구조체는 `currentLevel` 이라는 '저장 인스턴스 속성' 도 정의하는데, 이는 채널의 현재 음향 단계를 `0` 에서 `10` 까지의 크기로 표현합니다.

`currentLevel` 속성은 `didSet` 속성 관찰자를 가지고 있어서 `currentLevel` 의 값이 설정될 때마다 그 값을 검사합니다. 이 관찰자는 두 가지 검사를 수행합니다:

* 새 `currentLevel` 값이 `thresholdLevel` 에서 허용된 것보다 큰 경우, 이 '속성 관찰자' 는 `currentLevel` 을 `thresholdLevel` 까지로 쳐냅니다.
* (쳐낸 후의) 새 `currentLevel` 값이 이전에 `AudioChannel` 인스턴스 중 _어떤 (any)_ 것에서든 수신한 값보다 높은 경우, 이 '속성 관찰자' 는 새 `currentLevel` 값을 `maxInputLevelForAllChannels` 타입 속성에 저장합니다.

> 이 두 검사 중 첫 번째에서, `didSet` 관찰자는 `currentLevel` 을 다른 값으로 설정합니다. 이것은, 하지만, '관찰자' 를 다시 호출하지 않습니다.

`AudioChannel` 구조체로 새로이 `leftChannel` 과 `rightChennel` 이라는 두 개의 음향 채널을 생성하여, 스테레오 음향 시스템에 대한 음향 단계를 표현할 수 있습니다:

```swift
var leftChannel = AudioChannel()
var rightChannel = AudioChannel()
```

_왼쪽 (left)_ 채널의 `currentLevel` 을 `7` 로 설정하면, `maxInputLevelForAllChannels` 타입 속성이 `7` 로 갱신되는 것을 볼 수 있습니다:

```swift
leftChannel.currentLevel = 7
print(leftChannel.currentLevel)
// "7" 을 출력합니다.
print(AudioChannel.maxInputLevelForAllChannels)
// "7" 을 출력합니다.
```

_오른쪽 (right)_ 채널의 `currentLevel` 을 `11` 로 설정하려고 하면, 오른쪽 채널의 `currentLevel` 속성이 최대 값 `10` 으로 쳐내지고, `maxInputLevelForAllChannels` 타입 속성이 `10` 으로 갱신되는 것을 볼 수 있습니다:

```swift
rightChannel.currentLevel = 11
print(rightChannel.currentLevel)
// "10" 을 출력합니다.
print(AudioChannel.maxInputLevelForAllChannels)
// "10" 을 출력합니다.
```

### 참고 자료

[^Properties]: 이 글에 대한 원문은 [Properties](https://docs.swift.org/swift-book/LanguageGuide/Properties.html) 에서 확인할 수 있습니다.

[^instance-variables]: 이부분은 오브젝티브-C 언어와 스위프트 언어의 차이점에 대한 설명이므로, 오브젝티브-C 언어에 대해 잘 모른다면 넘어가도 됩니다.

[^optional-setter]: 여기서의 'optional' 은 스위프트에 있는 '옵셔널 타입' 과는 상관이 없습니다. '계산 속성' 은 '설정자 (setter)' 를 가질 수도 있고 안가질 수도 있기 때문에 'optional setter' 라는 용어를 사용합니다.

[^cuboid]: 'cuboid' 는 수학 용어로 '직육면체' 를 의미하며, 모든 면이 직사각형으로 이루어진 기하학적 도형을 의미합니다. 이름이 'cuboid' 인 것은 'polyhedral graph (다면체 그래프; 일종의 기하학적인 구조?)' 가 'cube (정육면체)' 와 같기 때문이라고 합니다. 보다 자세한 내용은 위키피디아의 [Cuboid](https://en.wikipedia.org/wiki/Cuboid) 또는 [직육면체](https://ko.wikipedia.org/wiki/직육면체) 를 참고하기 바랍니다.

[^nonoverridden-computed-properties]: 본문에서는 '재정의 하지 않은 계산 속성 (nonoverridden computed properties)' 이라고 뭔가 굉장히 어려운 말을 사용했는데, 그냥 개발자가 직접 만든 계산 속성은 모두 이 '재정의 하지 않은 계산 속성' 입니다. 본문의 내용은, 일반적으로 자신이 직접 만든 '계산 속성' 에는 따로 '속성 관찰자' 를 추가할 필요가 없다는 의미입니다. '계산 속성' 은 말 그대로 자신이 직접 값을 계산하는 것으로 값의 변화를 자기가 직접 제어하는 셈입니다. 그러니까 굳이 값의 변화를 관찰할 필요가 없습니다.

[^property-wrapper]: 굳이 복잡하게 '속성 포장 (property wrapper)' 를 왜 사용하는지 의문이 들 수 있습니다. 사실, 스위프트에서 '속성 포장' 을 사용하는 이유는 스위프트 언어 외부에서 지원하는 기능을 사용하기 위함입니다. iOS 라면 쓰레드 관리는 [GCD (Grand_Central_Dispatch)](https://en.wikipedia.org/wiki/Grand_Central_Dispatch) 를 이용하고, DB 는 [SQLite](https://www.sqlite.org/index.html) 를 이용할 수 있을 것입니다. 이들은 스위프트 외부에 존재하므로 '속성 포장 (property wrapper)' 을 통해서 관리 기능을 일종의 '위임 (delegation)' 하게 한다고 볼 수 있습니다.
