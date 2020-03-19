---
layout: post
comments: true
title:  "Swift 5.2: Strings and Characters (문자열과 문자)"
date:   2016-05-29 19:45:00 +0900
categories: Swift Grammar Strings Characters
---

> Apple 에서 공개한 [The Swift Programming Language (Swift 5.2)](https://docs.swift.org/swift-book/) 책의 [Strings and Characters](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html) 부분[^Strings-and-Characters]을 번역하고 정리한 글입니다.
>
> 현재 전체 중에서 번역 완료된 목록은 [Swift 5.2: Swift Programming Language (스위프트 프로그래밍 언어)](http://xho95.github.io/swift/programming/language/grammar/2017/02/27/The-Swift-Programming-Language.html) 에서 확인할 수 있습니다.

## Strings and Characters (문자열과 문자)

_문자열 (string)_ 문자가 연속되어 있는 것으로, `"hello, world"` 나 `"albatross"` 같은 것이 이에 해당합니다. 스위프트의 문자열은 `String` 타입으로 표현됩니다. `String` 의 내용물에 접근하는 방법은 `Character` 값의 '컬렉션 (collection)'[^collection] 도 포함하여 다양한 방법이 있습니다.

스위프트의 `String` 과 `Character` 타입은 코드에서 텍스트 작업을 할 때 빠르면서도 '유니코드에 부합하는 (Unicode-compliant)' 방법을 제공합니다. 문자열을 생성하고 조작하는 구문 표현은 가볍고 이해하기 쉬우며, '문자열 글자표현 구문 (string literal syntax)'[^string-literal-syntax] 은 C 언어와 비슷합니다. 문자열 연결은 두 문자열을 `+` 연산자로 결합하기만 하면 될 정도로 간단하며, 문자열의 '가변성 (mutability)' 은 스위프트의 다른 모든 값과 마찬가지로 상수인지 변수인지를 선택하는 것만으로 관리됩니다. 문자열을 사용하면 상수, 변수, '글자표현 (literals)'[^literals], 그리고 '표현식 (expressions)' 들을 더 큰 문자열에 삽입할 수도 있으며, 이 과정을 일컬어 '문자열 보간법 (string interpolation)'[^interpolation] 이라고 합니다. 이것으로 표시, 저장, 출력할 때 필요한 문자열을 아주 쉽게 만들 수 있습니다.

이렇게 간단한 구문 표현을 사용하면서도, 스위프트의 `String` 타입은 빠르고, 현대적인 문자열로 구현되었습니다. 모든 문자열은 '인코딩-독립적인 유니코드 문자들 (encoding-independent Unicode characters)' 로 구성되며, 다양한 유니코드 표현식으로 해당 문자들에 대한 접근을 지원합니다.

> 스위프트의 `String` 타입은 'Foundation' 프레임웍에 있는 `NSString` 클래스와 연동되어 (bridged) 있습니다. 'Foundation' 은 또한 `String` 을 확장해서 `NSString` 의 메소드들을 노출시킵니다. 이것은 'Foundation' 을 'import' 하면, 'casting (타입 변환)' 없이도 `String` 에서 `NSString` 메소드들 사용할 수 있음을 의미합니다.
>
> 'Foundation' 및 'Cocoa' 프레임웍과 `String` 을 같이 사용하는 방법에 대해서는 [Bridging Between String and NSString](https://developer.apple.com/documentation/swift/string#2919514) 에서 더 자세히 알 수 있습니다.

### String Literals (문자열 글자표현)

미리 정의된 `String` 값을 코드 내에 _문자열 글자표현 (string literals)_ 의 형태로 포함할 수 있습니다. '문자열 글자표현' 은 큰 따옴표 (`"`) 로 묶인 일련의 문자들을 말합니다.

문자열 글자표현은 상수나 변수의 초기 값으로 사용됩니다:

```swift
let someString = "Some string literal value"
```

`someString` 상수가 '문자열 글자표현 값' 으로 초기화되었기 때문에, 스위프트가 이를 `String` 타입으로 추론할 수 있음을 명심하기 바랍니다.

#### Multiline String Literals (여러 줄짜리 문자열 글자표현)

여러 줄에 걸쳐있는 문자열이 필요한 경우, '여러 줄짜리 문자열 글자표현 (multiline string literal)'-세 개의 큰 따옴표로 묶인 일련의 문자들-을 사용하십시오:

```swift
let quotation = """
The White Rabbit put on his spectacles. "Where shall I begin,
please your Majesty?" he asked.

"Begin at the beginning," the King said gravely, "and go on
till you come to the end; then stop."
"""
```

'여러 줄짜리 문자열 글자표현' 은 여는 따옴표와 닫는 따옴표 사이의 모든 줄도 포함합니다. 문자열은 여는 따옴표 (`"""`) 다음의 첫 번째 줄에서 시작하고 닫는 따옴표 앞의 줄에서 끝나며, 이는 아래에 있는 문자열은 어느 것도 '줄 바꿈 (line break)' 으로 시작하거나 끝나지 않음을 의미합니다:

```swift
let singleLineString = "These are the same."
let multilineString = """
These are the same.
"""
```

'줄 바꿈' 으로 시작하거나 끝나는 '여러 줄짜리 문자열 글자표현' 을 만들려면, 첫 줄 또는 마지막 줄에 빈 줄을 쓰면 됩니다. 예를 들면 다음과 같습니다:

```swift
let lineBreaks = """

This string starts with a line break.
It also ends with a line break.

"""
```

'여러 줄짜리 문자열 (multiline string)' 은 들여쓰기를 해서 주변 코드와 위치를 맞출 수 있습니다. 닫는 따옴표 (`"""`) 앞에 있는 공백은 스위프트가 모든 줄에서 그 만큼의 공백을 무시하도록 합니다. 하지만, 줄 맨 앞에 닫는 따옴표 앞에 있는 것보다 더 많은 공백을 입력하면, 그 공백은 문자열에 포함됩니다.

![Indentation](/assets/Swift/Swift-Programming-Language/String-and-Characters-indent.jpg)

위의 예에서, '여러 줄짜리 문자열 글자표현 (multiline string literal)' 전체가 들여쓰기되어 있지만, 문자열의 첫 줄과 마지막 줄은 공백으로 시작하지 않습니다. 가운데 줄은 닫는 따옴표보다 더 많이 들여쓰기 되어 있으므로, 이것만 추가로 4칸 들여쓰기로 시작합니다.

#### Special Characters in String Literals (문자열 글자표현 속의 특수 문자)

'문자열 글자표현' 은 다음의 특수 문자를 포함할 수 있습니다:

* (본래의 의미를) '벗어난 (escaped)[^escaped] 특수 문자' 들, `\0` (null-널 문자), `\\` (backslash-백슬래쉬), `\t` (가로 tab-탭), `\n` (line feed-줄 먹임), `\r` (carriage-캐리지 리턴), `\"` (큰 따옴표) 그리고 `\'` (작은 따옴표)
* 임의의 '유니코드 크기 값 (Unicode scalar[^scalar] value)', `\u{`_n_`}` 의 형태로 작성하며, 여기서 _n_ 은 1~8 자리의 16진수 값입니다. (유니코드는 아래의 [Unicode]() 에서 설명합니다.)

아래의 코드에서 특수 문자에 대한 네 가지 예를 보였습니다. `wiseWords` 상수는 두 개의 'escaped (벗어난)' 큰 따옴표를 담고 있습니다. `dollarSign`, `blackHeart` 그리고 `sparklingHeart` 상수는 '유니코드 크기 양식 (Unicode scalar format)' 을 보여줍니다:

```swift
let wiseWords = "\"Imagination is more important than knowledge\" - Einstein"
// "Imagination is more important than knowledge" - Einstein
let dollarSign = "\u{24}"         // $, 유니코드 크기 값 U+0024
let blackHeart = "\u{2665}"       // ♥, 유니코드 크기 값 U+2665
let sparklingHeart = "\u{1F496}"  // 💖, 유니코드 크기 값 U+1F496
```

'여러 줄짜리 문자열 글자표현 (multiline string literals)' 은 하나가 아닌 세 개의 큰 따옴표를 사용하므로, 여러 줄짜리 문자열 글자표현 안에 큰 따옴표 (`"`) 를 포함할 때는 'escaping (본래 의미를 벗어나게)'[^escaping] 할 필요가 없습니다. 여러 줄짜리 문자열에 `"""` 텍스트를 포함시키려면, 최소한 하나 이상의 따옴표를 'escape (본래 의미를 벗어나게)' 만들어야 합니다. 예를 들면 다음과 같습니다:

```swift
let threeDoubleQuotationMarks = """
Escaping the first quotation mark \"""
Escaping all three quotation mark \"\"\"
"""
```

#### Extended String Delimiters (확장된 문자열 구분자)

'문자열 글자표현 (string literal)' 을 _확장된 구분자 (extended delimiters)_ 안에 배치하면, 문자열에 특수 문자를 포함시키면서 효과는 발현 안되게 할 수 있습니다. 이것은 문자열을 따옴표 (`"`) 안에 넣고, 번호 기호 (`#`)[^number-sign] 로 감싸면 됩니다. 예를 들어, '문자열 글자표현' `#"Line 1\nLine 2"#` 를 출력하면 문자열이 두 줄로 출력되는 대신 '줄 바꿈 escape sequence (이스케잎 시퀀스)'인 (`\n`) 가 그대로 출력됩니다.

'문자열 글자표현 (string literal)' 에 있는 문자의 특수 효과를 사용하고 싶을 때는, 문자열 내에서 'escape (이스케잎)' 문자 (`\`) 뒤에 같은 개수의 번호 기호를 붙여주면 됩니다. 예를 들어, 문자열이 `#"Line 1\nLine 2"#` 인데, 줄을 바꾸고 싶으면 `#"Line 1\#nLine 2"#` 라고 하면 됩니다. 마찬가지로 `###"Line 1\###nLine 2"###` 도 줄 바꿈이 일어납니다.

'확장된 구분자' 로 생성한 '문자열 글자표현 (string literal)' 역시 '여러 줄짜리 문자열 글자표현' 이 될 수 있습니다. '확장된 구분자' 를 사용하면 '여러 줄짜리 문자열' 에 `"""` 텍스트를 넣을 수 있는데, 이 때 본래 가진 '글자표현 (literal) 을 끝낸다' 는 기본 기능을 뒤엎고 (overriding), 단순히 텍스트로 넣을 수 있습니다. 예를 들면 다음과 같습니다:

```
let threeMoreDoubleQuotationMarks = #"""
Here are three more double quotes: """
"""#
```

### Initializing an Empty String (빈 문자열 초기화하기)

더 긴 문자열을 만들기 위한 시작점으로 빈 `String` 을 만들려면, 변수에 '빈 문자열 글자표현 (empty string literal)' 을 할당하거나, '초기화 구문 표현 (initializer syntax)' 을 써서 새로운 `String` 인스턴스를 초기화하면 됩니다:

```swift
var emptyString = ""                // 빈 문자열 글자 표현 (empty string literal)
var anotherEmptyString = String()   // 초기화 구문 표현 (initializer syntax)
// 위 두 문자열은 모두 비어 있으며, 서로 동등합니다.
```

`String` 값이 비어있는지 확인하려면, 불린 (Boolean) 속성인 `isEmpty` 를 검사하면 됩니다:

```swift
if emptyString.isEmpty {
    print("Nothing to see here")
}
// "Nothing to see here" 를 출력합니다.
```

### String Mutability (문자열 가변성)

특정한 `String` 이 수정 (또는 _변경-mutated_) 가능한지를 지정하려면, 그것을 변수에 (이러면 수정 가능함) 할당하거나, 상수에 (이러면 수정 불가능함) 할당하면 됩니다:

```swift
var variableString = "Horse"
variableString += " and carriage"
// variableString 은 이제 "Horse and carriage" 입니다.

let constantString = "Highlander"
constantString += " and another Highlander"
// 이것은 컴파일-시간에 -상수 문자열은 수정될 수 없다는 (a constant string cannot be modified -에러를 발생시킵니다.
```

> '오브젝티브-C' 와 'Cocoa' 에서의 문자열 가변성 지정 방식은 좀 다른데, 이들은 두 개의 클래스 (`NSString` 와 `NSMutableString`) 중에서 선택하는 것으로써 문자열이 변할 수 있는지를 지정합니다.

### Strings Are Value Types (문자열은 값 타입입니다)

스위프트의 `String` 타입은 '_값 타입 (value type)_'[^value-type] 입니다. 이것은 새로운 `String` 값을 만들고서, 이를 함수나 메소드에 전달하거나, 상수나 변수에 할당할 때, 이 `String` 값이 _복사 (copied)_ 된다는 것을 말합니다. 각각의 경우에, 기존 `String` 값에 대한 새 복사본이 만들어져서, 원래 버전 대신, 이 복사본이 전달되거나 할당됩니다. 값 타입에 대해서는 [Structure and Enumerations Are Value Types](https://docs.swift.org/swift-book/LanguageGuide/ClassesAndStructures.html#ID88) 에 설명되어 있습니다.

스위프트의 `String` 이 '기본적으로-복사 (copy-by-default)' 행동을 한다는 것은 함수나 메소드로 `String` 값을 전달받을 때, 어디서 왔든 신경쓸 필요 없이, 그 `String` 값을 온전히 가지게 됐음을 분명히 한다는 것입니다. 전달받은 문자열은 본인이 직접 수정하지 않는 이상 수정될 일이 없다고 확신헤도 됩니다.

한편, 스위프트의 컴파일러는 문자열 처리를 최적화하기 때문에 실제 복사는 꼭 필요할 때에만 일어납니다.[^optimize-string] 이것은 값 타입인 문자열을 사용하더라도 항상 뛰어난 성능을 보장받을 수 있다는 의미입니다.

### Working with Characters (문자 다루기)

`String` 에 있는 개별 `Character` 값에 접근하려면, `for-in` 반복문으로 문자열에 '동작을 반복 적용 (interating over)' 시키면 됩니다:

```swift
for character in "Dog!🐶" {
    print(character)
}
// D
// o
// g
// !
// 🐶
```

`for-in` 반복문은 [For-In Loope (For-In 반복문)](https://docs.swift.org/swift-book/LanguageGuide/ControlFlow.html#ID121) 에 설명되어 있습니다.

다른 방법으로, `Character` 타입 '주석 (annotation)'[^annotation] 을 쓰면 '단일-문자 문자열 글자표현 (single-character string literal)' 으로 독립된 `Character` 상수나 변수를 만들 수도 있습니다:

```swift
let exclamationMark: Character = "!"
```

`String` 값은 초기자의 인자로 `Character` 값의 배열을 전달하는 것으로도 생성할 수 있습니다:

```swift
let catCharacters: [Character] = ["C", "a", "t", "!", "🐱"]
let catString = String(catCharacters)
print(catString)
// "Cat!🐱" 을 출력합니다.
```

### Concatenating Strings and Characters (문자열 및 문자 연결하기)

`String` 값을 '더하기 연산자 (`+`)' 로 서로 더하기-또는 _연결 (concatenated)_ 하여 새 `String` 값을 만들 수 있습니다:

```swift
let string1 = "hello"
let string2 = " there"
var welcome = string1 + string2
// welcome 은 이제 "hello there" 와 같습니다.
```

`String` 값을 '더하고 할당하기 연산자 (`+=`)' 로 기존 `String` 변수에 덧붙일 수 있습니다:

```swift
var instruction = "look over"
instruction += string2
// instruction 은 이제 "look over there" 와 같습니다.
```

`Character` 값을 `String` 변수에 덧붙이려면 `String` 타입의 `append()` 메소드를 사용하면 됩니다:

```swift
let exclamationMark: Character = "!"
welcome.append(exclamationMark)
// welcome 은 이제 "hello there!" 와 같습니다.
```

> `String` 이나 `Character` 를 기존 `Character` 변수에 덧붙일 수는 없으며, 이는 `Character` 값은 반드시 단 하나의 문자만 가질 수 있기 때문입니다.

'여러 줄짜리 문자열 글자표현' 으로 더 긴 줄의 문자열을 만들 때, 문자열의 모든 줄이 마지막 줄도 포함해서, 줄 바꿈으로 끝나기를 원할 것입니다. 예를 들면 다음과 같습니다:

```swift
let basStart = """
one
two
"""

let end = """
three
"""

print(basStart + end)
// 다음의 두 줄을 출력합니다:
// one
// twothree

let goodStart = """
one
two

"""

print(goodStart + end)
// 다음의 세 줄을 출력합니다:
// one
// two
// three
```

위의 코드에서, `badStart` 와 `end` 를 연결하니 두 줄짜리 문자열이 만들어졌는데, 이는 원하는 결과가 아닙니다. 왜냐면 `badStart` 의 마지막 줄이 줄 바꿈으로 끝난게 아니라서, 그 줄이 `end` 의 첫 줄과 붙어버렸기 때문입니다. 이와는 다르게, `goodStart` 의 두 줄은 모두 줄 바꿈으로 끝나므로, `end` 와 결합해도 결과는 예상한 대로 세 줄이 됩니다.

### String Interpolation (문자열 보간법)

* string interpolation
    * a new `String` value from a mix of constants, variables, literals, and expressions
    * each item is wrapped in a pair of parentheses, prefixed by a backslash

```swift
let multiplier = 3

let message = "\(multiplier) times 2.5 is \(Double(multiplier) * 2.5)"

// message is "3 times 2.5 is 7.5"
```

* this placeholder is replaced with the actual value

> the expressions
 - cannot contain an unescaped backslash(`\`), a carriage return, or a line feed
 - can contain other string literals


### Unicode (유니코드)

* **Unicode**
    * an international standard for encoding, representing, and processing text in different writing systems
    * represent almost any character from any language in a standardized form
- Swift's `String`, `Character` : fully Unicode-compliant


#### Unicode Scalars (유니코드 크기 값)

* Swift's native `String` type : built from `Unicode scalar` values
* a Unicode scalar : a unique 21-bit number for a character or modifier
    * ex) LATIN SMALL LETTER A ("a") : `U+0061`

* not all 21-bit Unicode scalars are assigned to a character : reserved for future assignment
* scalars typically have a name


#### Extended Grapheme Clusters (확장된 음소 덩어리)

* every instance of Swift's `Character` type : a single **extended grapheme cluster**
* an **extended grapheme cluster** :
    * a sequence of one or more Unicode scalars
    * produce a single human-readable character
- the letter `é`
    - represented as the single Unicode scalar `é` : `U+00E9`
    - represented as a pair of scalars : `e` (`U+0065`) + `\ ́` (`U+0301`)
* In both cases, the letter `é` - a single Swift `Character` value

```swift
let eAcute: Character = "\u{E9}"                // é

let combinedEAcute: Character = "\u{65}\u{301}" // e followed by  ́

// eAcute is é, combinedEAcute is é
```

* Extended grapheme clusters : a flexible way
    * ex) Hangul syllables : represented as either a precomposed or decomposed sequence

```swift
let precomposed: Character = "\u{D55C}"                 // 한

let decomposed: Character = "\u{1112}\u{1161}\u{11AB}"  // ᄒ, ᅡ, ᆫ

// precomposed is 한, decomposed is 한
```

* scalars for enclosing marks

```swift
let enclosedEAcute: Character = "\u{E9}\u{20DD}"

// enclosedEAcute is é⃝
```

* unicode scalars for regional indicator symbols : make a singe `Character` value
    * `U` (`U+1F1FA`) + `S` (`U+1F1F8`)

```swift
let regionalIndicatorForUS: Character = "\u{1F1FA}\u{1F1F8}"

// regionalIndicatorForUS is 🇺🇸
```


### Counting Characters (문자 개수 살리기)

* to retrieve a count of the `Character` values : the `count` property of the string's `characters`

```swift
let unusualMenagerie = "Koala 🐨, Snail 🐌, Penguin 🐧, Dromedary 🐪"

print("unusualMenagerie has \(unusualMenagerie.characters.count) characters")

// Prints "unusualMenagerie has 40 characters"
```

* string concatenation and modification may not always affect a string's character count
    * extended grapheme clusters for `Character` values
- ex) cafe + `\ ́` (`U+0301`) = a character count of `4`

```swift
var word = "cafe"

print("the number of characters in \(word) is \(word.characters.count)")

// Prints "the number of characters in cafe is 4"

word += "\u{301}"       // COMBINING ACUTE ACCENT, U+0301

print("the number of characters in \(word) is \(word.characters.count)")

// Prints "the number of characters in café is 4"
```

> Extended grapheme clusters : composed of one or more Unicode scalars

> different characters and different representations of the same character can require different amounts of memory to store

> characters in Swift do not each take up the same amount of memory within a string's representation

> the number of characters in a string cannot be calculated without iterating through the string to determine its extended grapheme cluster boundaries

> `characters` property must iterate over the Unicode scalars in the entire string in order to determine the characters for that sting : long string values - be aware that !

> the count of the characters : not always the same as the `length` property of an `NSString`

> the length of an `NSString` : the number of 16-bit code units within the string's UTF-16 representation


### Accessing and Modifying a String (문자열에 접근하고 수정하기)

* access and modify a string : its methods and properties, or by using subscript syntax


#### String Indices (문자열 색인)

* each `String` value : has an associated index type - `String.Index`
    * the position of each `Character` in the string
- to determine which `Character` is at a position : iterate over each Unicode scalar from the start or end  of that `String`
- Swift strings cannot be indexed by integer values.
* `startIndex` : the position of the first `Character` of a `String`
* `endIndex` : the position after the last character in a `String` - `endIndex` isn't a valid argument to a string's subscript
* `String` is empty : `startIndex` and `endIndex` are equal
- a `String.Index` value
    - `predecessor()` : its immediately preceding index
    - `successor()` : its immediately succeeding index
- any index in a `String`
    - by chaining these methods together
    - by using the `advancedBy(_:)` method
- access an index outside of a string's range : trigger a runtime error

* subscript syntax : access the `Character` at a particular `String` index

```swift
let greeting = "Guten Tag!"

greeting[greeting.startIndex]

// G

greeting[greeting.endIndex.predecessor()]

// !

greeting[greeting.startIndex.successor()]

// u

let index = greeting.startIndex.advancedBy(7)

greeting[index]

// a
```

* access a `Character` at an index outside of a string's range : trigger a runtime error

```swift
// greeting[greeting.endIndex]     // error
// greeting.endIndex.successor()   // error
```

* `indices` property of the `characters` : create a `Range` of all of the indexes

```swift
for index in greeting.characters.indices {
    print("\(greeting[index]) ", terminator: "")
}

// prints "G u t e n   T a g ! "
```


#### Inserting and Removing (삽입 및 제거하기)

* `insert(_:atIndex:)` : to insert a character into a string at a specified index

```swift
var welcome_2 = "hello"

welcome_2.insert("!", atIndex: welcome_2.endIndex)

// welcome_2 now equals "hello!"
```

* `insertContentsOf(_:at:)` : to insert the contents of another string at a specified index

```swift
welcome_2.insertContentsOf(" there".characters, at: welcome_2.endIndex.predecessor())

// welcome_2 now equals "hello there!"
```

* `removeAtIndex(_:)` : to remove a character from a string at a specified index

```swift
welcome_2.removeAtIndex(welcome_2.endIndex.predecessor())

// welcome now equals "hello there"
```

* `removeRange(_:)` : to remove a substring at a specified range

```swift
let range = welcome_2.endIndex.advancedBy(-6)..<welcome_2.endIndex

welcome_2.removeRange(range)

// welcome now equals "hello"
```

### Substrings (하위 문자열)

### Comparing Strings (문자열 비교하기)

* three ways to compare textual values
    * string and character equality
    * prefix equality
    * suffix equality


#### String and Character Equality (문자열 및 문자 동등성)

* checked with the "equal to" operator (`==`) and the "not equal to" operator (`!=`)

```swift
let quotation = "We're a lot alike, you and I."

let sameQuotation = "We're a lot alike, you and I."

if quotation == sameQuotation {
    print("These two strings are considered equal")
}

// Prints "These two strings are considered equal."
```

* two `String` values (or two `Character` values) are considered equal
    * extended grapheme clusters are **canonically equivalent**
    * **canonically equivalent** : the same linguistic meaning and appearance
    * even if they are composed from different Unicode scalars
- ex) `é` (`U+00E9`) == `e` (`U+0065`) + `\ ́` (`U+0301`)

```swift
// "Voulez-vous un café?" using LATIN SMALL LETTER E WITH ACUTE

let eAcuteQuestion = "Voulez-vous un caf\u{E9}?"

// "Voulez-vous un café?" using LATIN SMALL LETTER E and COMBINING ACUTE ACCENT

let combinedEAccuteQuestion = "Voulez-vous un caf\u{65}\u{301}?"

if eAcuteQuestion == combinedEAccuteQuestion {
    print("These two strings are considered equal")
}

// Prints "These two strings are considered equal"
```

* ex) `A` (`U+0041`) used in English is not equivalent to `А` (`U+0410`) used in Russian.
* the characters do not have the same linguistic meaning

```swift
let latinCapitalLetterA: Character = "\u{41}"

let cyrillicCapitalLetterA: Character = "\u{0410}"

if latinCapitalLetterA != cyrillicCapitalLetterA {
    print("These two characters are not equivalent")
}

// Prints "These two characters are not equivalent"
```

> note:
 String and character comparisons in Swift are not locale-sensitive.


#### Prefix and Suffix Equality (접두사 및 접미사 동등성)

* to check whether a string has a particular string prefix or suffix : `hasPrefix(_:)`, `hasSuffix(_:)` methods
* both take a single argument of type `String` and return a Boolean value
- the first two acts of Shakespeare's **Romeo and Juliet**

```swift
let romeoAndJuliet = [
    "Act 1 Scene 1: Verona, A public place",
    "Act 1 Scene 2: Capulet's mansion",
    "Act 1 Scene 3: A room in Capulet's mansion",
    "Act 1 Scene 4: A street outside Capulet's mansion",
    "Act 1 Scene 5: The Great Hall in Capulet's mansion",
    "Act 2 Scene 1: Outside Capulet's mansion",
    "Act 2 Scene 2: Capulet's orchard",
    "Act 2 Scene 3: Outside Friar Lawrence's cell",
    "Act 2 Scene 4: A street in Verona",
    "Act 2 Scene 5: Capulet's mansion",
    "Act 2 Scene 6: Friar Lawrence's cell"
]
```

* `hasPrefix(_:)` : to count the number of scenes in Act 1

```swift
var act1SceneCount = 0

for scene in romeoAndJuliet {
    if scene.hasPrefix("Act 1") {
        act1SceneCount += 1
    }
}

print("There are \(act1SceneCount) scenes in Act 1")

// Prints "There are 5 scenes in Act 1"
```

* `hasSuffix(_:)` : to count the number of scenes that take place in or around Capulet's mansion and Friar Lawrence's cell

```swift
var mansionCount = 0
var cellCount = 0

for scene in romeoAndJuliet {
    if scene.hasSuffix("Capulet's mansion") {
        mansionCount += 1
    } else if scene.hasSuffix("Friar Lawrence's cell") {
        cellCount += 1
    }
}

print("\(mansionCount) mansion scenes; \(cellCount) cell scenes")

// Prints "6 mansion scenes; 2 cell scenes"
```

> note:
 `hasPrefix(_:)`, `hasSuffix(_:)` : a character-by-character canonical equivalence comparison between the extended grapheme clusters in each string


### Unicode Representations of Strings (문자열의 유니코드 표현)

* Unicode scalars : encoded in one of several Unicode-defined **encoding forms**
* each form encodes the string in small     - **code units**
    * the UTF-8 encoding form : encodes a string as 8-bit code units
    * the UTF-16 encoding form : encodes a string as 16-bit code units
    * the UTF-32 encoding form : encodes a string as 32-bit code units
- access a `String` value in one of three other Unicode-compliant representations
    - `utf8` property : a collection of UTF-8 code units
    - `utf16` property : a collection of UTF-16 code units
    - `unicodeScalars` property : a collection of 21-bit Unicode scalar values - equivalent to the string's UTF-32 encoding form
* each example below shows a different representation of the following string

```swift
let dogString = "Dog!!🐶"
```


#### UTF-8 Representation

* `utf8` : access a UTF-8 representation of a `String`
    * type : `String.UTF8View` - a collection of unsigned 8-bit values (`UInt8`)

```swift
for codeUnit in dogString.utf8 {
    print("\(codeUnit) ", terminator: "")
}

print("")

// 68 111 103 226 128 188 240 159 144 182
```

* `68, 111, 103` : `D`, `o`, and `g`
* `226, 128, 188` : `!!`
* `240, 159, 144, 182` : `🐶`


#### UTF-16 Representation

* `utf16` : access a UTF-8 representation of a `String`
    * type : `String.UTF16View` - a collection of unsigned 8-bit values (`UInt16`)

```swift
for codeUnit in dogString.utf16 {
    print("\(codeUnit) ", terminator: "")
}

print("")

// 68 111 103 8252 55357 56374
```

* `68, 111, 103` : `D`, `o`, and `g` - the same values as in the string's UTF-8 representation
* `8252` : `!!` - a decimal equivalent of the hexadecimal value `203C`
* `55357, 56374` : `🐶` - a UTF-16 surrogate pair


#### Unicode Scalar Representation

* `unicodeScalars` : access a Unicode scalar representation of a `String`
    * type : `String.UnicodeScalarView` - a collection of `UnicodeScalar`
    * each `UnicodeScalar` has a `value` property : return the scalar's 21-bit value - a `UInt32` value

```swift
for scalar in dogString.unicodeScalars {
    print("\(scalar.value) ", terminator: "")
}

print("")

for scalar in dogString.unicodeScalars {
    print("\(scalar) ")
}

// 68 111 103 8252 128054
```

* `68, 111, 103` : `D`, `o`, and `g` - the same values as in the string's UTF-8 representation
* `8252` : `!!` - a decimal equivalent of the hexadecimal value `203C`
* `128054` : `🐶` - a decimal equivalent of the hexadecimal value `1F436`

* an alternative to querying `value` properties : `UnicodeScalar`

### 참고 자료

[^Strings-and-Characters]: 원문은 [Strings and Characters](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html) 에서 확인할 수 있습니다.

[^collection]: '컬렉션 (collection)' 은 스위프트에서 특정한 값들의 집합을 묘사하는 '집합체' 타입입니다. 보다 자세한 내용은 [Collection Types (집합체 타입)](http://xho95.github.io/swift/grammar/collection/array/set/dictionary/2016/06/06/Collection-Types.html) 을 참고하기 바랍니다.

[^string-literal-syntax]: '문자열 글자표현 구문 (string literal syntax)' 은 말은 어렵지만 개념은 아주 간단합니다. `let greeting = "hello"` 와 같은 문장에서 `"hello"` 가 바로 '문자열 글자표현 구문 (string literal syntax)' 입니다. 이 책에서 말하는 것은 스위프트에서 사용하는 이 '문자열 글자표현 구문' 이 사실상 C 언어와 같아서 이해하기 쉽다는 의미입니다.

[^literals]: 여기서 '글자표현 (literals)' 는 '글자로 표현된 실제 값' 을 의미하며, `let a = 3.14` 에서는 `3.14` 라는 `Double` 값이 되고, `let b = "hello"` 에서는 `"hello"` 라는 `String` 값이 됩니다. 즉 '글자표현 (literals)' 에서 값의 타입은 그 값이 실제로 표현하는 것이 무엇인지에 따라 달라집니다.

[^interpolation]: '보간법 (interpolation)' 은 원래 수학 용어로 그래프 상에서 두 점 사이의 값을 근사적으로 구해서 채워넣는 방법을 말합니다. 'string interpolation' 은 굳이 직역하면 '문자열 삽입법' 등으로 옮길 수 있겠지만, 'interpolation' 은 원래부터 '보간법' 이라는 말로 많이 사용하고 있으므로 그대로 '보간법' 을 사용하도록 합니다. '문자열 보간법' 은 한 문자열 중간에 다른 값을 문자열의 형태로 집어넣는 것으로 이해할 수 있습니다.

[^escaped]: 'escaped' 는 우리 말로 '벗어난' 을 의미하는 단어인데, 프로그래밍에서 'escaped special characters' 라고 하면 '(본래의 의미를) 벗어나서 다른 의미를 가지게된 특수 문자' 라는 의미가 됩니다. 예를 들어 `n` 이라고 하면, 그냥 하나의 영어 문자가 되지만, `\n` 이라고 하면 본래의 영어 단어의 의미를 벗어나서, `new line (feed)` 이라는 새로운 의미를 가지게 됩니다. 이렇게 문자 앞에 슬래쉬 (`\`) 를 붙여서 '본래 의미를 벗어난 다른 의미를 가지는 문자' 를 일컬어 'escaped characters' 라고 합니다.

[^scalar]: 'scalar' 는 원래 수학 용어로 '크기만을 가지는 값' 입니다. 여기서 'Unicode scalar value' 은 각각의 문자에 일대일 대응되는 '유니코드 크기 값' 을 의미합니다. 예를 들어, 문자는 `$` 는 유니코드 크기 값이 `U+0024` 에 해당합니다.

[^escaping]: 여기서 'escaping' 할 필요 없다는 말은 슬래쉬 (`\`) 기호를 붙일 필요가 없다는 것을 의미합니다.

[^number-sign]: '#' 은 영어로 'number sign' 이라고 하는데, 보통 우리 말로는 '샾 기호' 라고 알려져 있습니다. 하지만 실제 샾 기호와는 다르며 하나의 숫자를 의미합니다. 여기서는 '번호 기호' 라고 옮기도록 합니다.

[^value-type]: '값 타입 (value type)' 이라는 말은, 프로그래밍 용어에서 '깊은 복사' 와 '옅은 복사' 라는 말이 있는데, 이 중에서 복사 시의 기본 동작이 '깊은 복사' 인 타입이라고 이해할 수 있습니다.

[^optimize-string]: 이 말은 기본적으로 `String` 은 '깊은 복사' 를 한다고는 하지만, 만약 전달받은 `String` 을 상수처럼 사용할 경우, 굳이 값을 복사할 필요가 없으므로 스위프트가 성능 최적화를 해서, 실제 복사를 안할 수도 있다는 말입니다.

[^annotation]: 'annotation' 은 '주석' 이라는 말로 옮길 수 있는데, 스위프트에서 '주석 (annotaion)' 이라 하면 `let a: Int = 10` 에서 `Int` 처럼 타입을 지정해 주는 것을 말합니다.
