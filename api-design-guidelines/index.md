---
layout: page
title: API Design Guidelines
official_url: https://swift.org/documentation/api-design-guidelines/
redirect_from: /documentation/api-design-guidelines.html
---
<!-- {% comment %}
The width of <pre> elements on this page is carefully regulated, so we
can afford to drop the scrollbar boxes. 
{% endcomment %} -->
<style>
article pre {
    overflow: visible;
}
</style>

<!-- {% comment %}
Define some variables that help us build expanding detail sections
without too much boilerplate.  We use checkboxes instead of 
<details>...</details> because it allows us to:

  * Write CSS ensuring that details aren't hidden when printing.
  * Add a button that expands or collapses all sections at once.
{% endcomment %} -->
{% capture expand %}{::nomarkdown}
<input type="checkbox" class="detail">
{:/nomarkdown}{% endcapture %}
{% assign detail = '<div class="more" markdown="1">' %}
{% assign enddetail = '</div>' %}

<div class="info screenonly" markdown="1">
빠른 참조 기능을 지원하기 위해, 가이드 라인의 세부 사항 대부분은 자세히 보기 기능을 통해 제공됩니다.
이 페이지를 출력할 경우, 모든 세부 사항이 함께 제공됩니다.
<input type="button" id="toggle" value="Expand all details now" onClick="show_or_hide_all()" />
</div>

## Table of Contents
{:.no_toc}

* TOC
{:toc}

## 기본 개념

* 가장 중요한 목표는 **사용 시점에서의 명료성**입니다.
  메소드, 속성과 같은 개체들(Entities)은 한 번만 선언되지만, 반복적으로 사용됩니다.
  API는 이러한 개체들을 이해하기 쉽고 간결하게 만드는데 중점을 두고 작성해야 합니다.
  API 설계에 대한 평가는 선언부를 읽는 것만으로는 충분하지 않습니다.
  사용사례에서 문맥상 명확하게 이해되는지를 기준으로 평가해야 합니다.
  {:#clarity-at-the-point-of-use}

* **명료성은 간결성보다 더 중요합니다.**
  Swift 코드를 간결하게 작성할 수 있지만,
  문자들 몇 개만 사용해서 가능한 가장 적은 양의 코드를 작성하는 것이 목표가 아닙니다.
  Swift 코드의 간결함은 강력한 type 시스템과 boilerplate 코드를 줄여주는 기능들이 제공하는
  부수적인 효과입니다.
  {:#clarity-over-brevity}

* 모든 선언문에 **문서화용 주석(documentation comments)을 작성하세요.**
  API 문서를 작성하면서 얻은 통찰력은 API 설계에 지대한 영향을 미칠 수 있습니다.
  다음으로 미루지 말고 꼭 주석을 달아주세요.
  {:#write-doc-comment}

  <div class="warning" markdown="1">
  간단한 용어로 API 기능을 설명하지 못한다면, **당신의 API 설계는 문제가 있을 가능성이 높습니다.**
  </div>
  
  {{expand}}
  {{detail}}
  {% assign ref = 'https://developer.apple.com/library/prerelease/mac/documentation/Xcode/Reference/xcode_markup_formatting_ref/' %}
  {% capture SymbolDoc %}{{ref}}SymbolDocumentation.html#//apple_ref/doc/uid/TP40016497-CH51-{% endcapture %}

  * **Swift 에서 지원하는 [Markdown 언어]({{ref}})를 사용하세요.**

  * 선언된 개체(entity)를 설명하는 **요약으로 시작**하세요.
    API는 선언과 요약을 통해 완전히 이해되는 경우가 많습니다.

    ~~~ swift
    /// **같은 요소를 포함하는 `self`의 "view"를 역순으로 반환.**
    func reversed() -> ReverseCollection<Self>
    ~~~

    {{expand}}
    {{detail}}

    * **요약에 초점을 맞추세요**. 요약은 매우 중요한 부분입니다.
      많은 우수한 코드 주석은 훌륭한 요약문을 가지고 있습니다.

    * 가능하면 **한 개의 절을 사용하고**, 마침표로 끝내세요.
      완전한 문장을 사용하지 마세요.

    * **함수 또는 메소드가 *어떤 일을 하는지*, *어떤 것을 반환하는지* 설명하고,**
      null 효과와 `Void` 반환은 설명을 생략하세요:

      ~~~ swift
      /// `self` 시작부분에 `newHead`를 **삽입**.
      mutating func prepend(newHead: Int)

      /// `self`의 요소를 동반하는 `head` 가 포함된 `List` 를 **반환**.
      func prepending(head: Element) -> List

      /// 비어있지 않다면 `self`의 첫 번째 요소를 **제거 및 반환하고**; 비어있다면 `nil`을 반환.
      mutating func popFirst() -> Element?
      ~~~

      주의 : 자주 사용되지는 않지만, `popFirst` 의 경우처럼 세미콜론을 사용해 여러 절로 이루어진 요약문을
      작성할 수도 있습니다.

    * **subscript 가 어떤 것에 *접근*하는지 설명합니다**:

      ~~~ swift
      /// `index` 번째 요소에 **접근**.
      subscript(index: Int) -> Element { get set }
      ~~~

    * **이니셜라이저가 무엇을 *생성*하는지 설명합니다**:

      ~~~ swift
      /// `x`를 `n`번 반복하는 인스턴스를 **생성**.
      init(count n: Int, repeatedElement x: Element)
      ~~~

    * 그 외의 경우, **선언된 개체*가* 무엇인지 설명합니다**.

      ~~~ swift
      /// 어떤 위치에서든 똑같이 효율적으로 삽입/제거할 수 있는 **컬렉션**.
      struct List {

        /// `self`의 **첫 번째 요소**, 또는 self가 비어있다면 `nil`
        var first: Element?
        ...
      ~~~

    {{enddetail}}

  * 경우에 따라, 여러 절과 글 머리 기호를 사용할 수 있습니다.
    공백 줄로 절을 나누고 완전한 문장을 사용합니다.

    ~~~ swift
    /// 표준 출력에 `items` 각 요소의 텍스트 표현을 작성합니다. <span class="graphic">←</span><span class="commentary"> 요약</span>
    ///                                             <span class="graphic">←</span><span class="commentary"> 빈 줄</span>
    /// 각 요소인 `x`의 텍스트 표현은 `String(x)` 표현식으로   <span class="graphic">←</span><span class="commentary"> 추가 설명</span>
    /// 제공됩니다.
    ///
    /// - **매개변수 seperator**: 요소 사이에 출력되는 텍스트      <span class="graphic">⎫</span>
    /// - **매개변수 terminator**: 끝부분에 출력되는 텍스트           <span class="graphic">⎬</span><span class="commentary"> <a href="{{SymbolDoc}}SW14">매개변수 부분</a></span>
    ///                                              <span class="graphic">⎭</span>
    /// - **주의**: 끝부분에 줄 바꿈을 출력하지 않으려면           <span class="graphic">⎫</span>
    ///   `terminator: ""`를 전달하세요.                <span class="graphic">⎟</span>
    ///                                              <span class="graphic">⎬</span><span class="commentary"> <a href="{{SymbolDoc}}SW13">기타 참고사항</a></span>
    /// - **참조**: `CustomDebugStringConvertible`,       <span class="graphic">⎟</span>
    ///   `CustomStringConvertible`, `debugPrint`.   <span class="graphic">⎭</span>
    public func print<Target: OutputStreamType>(
      items: Any..., separator: String = " ", terminator: String = "\n")
    ~~~

    {{expand}}
    {{detail}}

    * 요약문 외에 추가 정보를 제공할 때에는 많은 사람들이 이해할 수 있게
      **[symbol 문서 마크업]({{SymbolDoc}}SW1) 요소들을 사용하세요.**

    * **[symbol 커맨드 구문]({{SymbolDoc}}SW13) 을 익히고 활용하세요.** 
      Xcode와 같이 인기 있는 개발 도구는  
      다음 키워드로 시작하는 글 머리 기호(예: - Note)를 특별하게 취급합니다:

      | [Attention]({{ref}}Attention.html) | [Author]({{ref}}Author.html) | [Authors]({{ref}}Authors.html) | [Bug]({{ref}}Bug.html) |
      | [Complexity]({{ref}}Complexity.html) | [Copyright]({{ref}}Copyright.html) | [Date]({{ref}}Date.html) | [Experiment]({{ref}}Experiment.html) |
      | [Important]({{ref}}Important.html) | [Invariant]({{ref}}Invariant.html) | [Note]({{ref}}Note.html) | [Parameter]({{ref}}Parameter.html) |
      | [Parameters]({{ref}}Parameters.html) | [Postcondition]({{ref}}Postcondition.html) | [Precondition]({{ref}}Precondition.html) | [Remark]({{ref}}Remark.html) |
      | [Requires]({{ref}}Requires.html) | [Returns]({{ref}}Returns.html) | [SeeAlso]({{ref}}SeeAlso.html) | [Since]({{ref}}Since.html) |
      | [Throws]({{ref}}Throws.html) | [Todo]({{ref}}Todo.html) | [Version]({{ref}}Version.html) | [Warning]({{ref}}Warning.html) |

    {{enddetail}}

  {{enddetail}}

## 이름 지정

### 명확한 사용 활성화하기(Promote Clear Usage)

* 이름이 사용된 코드를 읽는 사람을 위해
  **모호성을 피하고자 필요한 모든 단어를 포함합니다**.

  {:#include-words-to-avoid-ambiguity}

  {{expand}}
  {{detail}}
  예를 들어, 컬렉션 내 주어진 위치에 있는 요소를 제거하는 메소드를 고려합니다.

  ~~~ swift
  extension List {
    public mutating func remove(at position: Index) -> Element
  }
  employees.remove(at: x)
  ~~~
  {:.good}

  메소드 서명에서 `at` 단어를 생략한다면, 삭제하려는 요소의 위치를
  나타내기 위해 `x`를 사용하는 것보다는 `x`와 같은 요소를
  검색하고 제거한다고 독자에게 암시할 수 있습니다.

  ~~~ swift
  employees.remove(x) // 불분명: x를 제거하는가?
  ~~~
  {:.bad}

  {{enddetail}}

* **불필요한 단어는 생략합니다.** 이름 내 모든 단어는 사용 장소에서
  두드러진 정보를 전달해야 합니다.
  {:#omit-needless-words}

  {{expand}}
  {{detail}}
  명확한 의도 또는 애매하지 않은 의미를 위해 더 많은 단어가 필요할 수 있지만,
  이는 독자가 이미 알고 있어 생략한 불필요한 정보입니다.
  특히, *단순히* 타입 정보를 *반복하는* 단어는 생략합니다.

  ~~~ swift
  public mutating func removeElement(member: Element) -> Element?

  allViews.removeElement(cancelButton)
  ~~~
  {:.bad}

  이 경우엔, `Element` 단어는 호출 장소에서 두드러지는 것을 추가하지 않습니다.
  이 API가 좀 더 낫습니다:

  ~~~ swift
  public mutating func remove(member: Element) -> Element?

  allViews.remove(cancelButton) // clearer
  ~~~
  {:.good}

  때때로, 반복 타입 정보는 모호성을 피하기 위해 필요하지만,
  일반적으로 타입보다는 매개변수의 *역할*을 설명하는 단어 사용이 더 낫습니다.
  자세한 내용은 다음 항목을 참조하세요.
  {{enddetail}}

* **역할에 따라 이름 변수, 매개 변수, 연관 타입**은 타입 제약사항보다 낫습니다.
  {:#name-according-to-roles}

  {{expand}}
  {{detail}}
  ~~~ swift
  var **string** = "Hello"
  protocol ViewController {
    associatedtype **View**Type : View
  }
  class ProductionLine {
    func restock(from **widgetFactory**: WidgetFactory)
  }
  ~~~
  {:.bad}

  이러한 방법으로 타입 이름 용도를 변경하는 것은 명확성과 표현력을 최적화하지 못합니다.
  대신, 개체의 *역할*을 표현하는 이름을 선택하도록 노력합니다.

  ~~~ swift
  var **greeting** = "Hello"
  protocol ViewController {
    associatedtype **ContentView** : View
  }
  class ProductionLine {
    func restock(from **supplier**: WidgetFactory)
  }
  ~~~
  {:.good}
  
  연관 타입은 프로토콜 이름이 역할인 프로토콜 제약사항으로 매우 밀접하게 결합되었다면,
  연관 타입 이름에 `Type`을 붙여 충돌을 피합니다.

  ~~~ swift
  protocol Sequence {
    associatedtype Iterator**Type** : Iterator
  }
  ~~~
  {{enddetail}}

* 매개변수의 역할을 명확하도록 **약 타입 정보를 보정합니다**.
  {:#weak-type-information}

  {{expand}}
  {{detail}}
  특히 매개변수 타입이 `NSObject`, `Any`, `AnyObject`이거나
  `Int` 또는 `String` 같은 기본 타입일 때, 사용 시점에 타입 정보와 상황은
  의도를 완전히 전달할 수 없을 수 있습니다. 이 예제에서, 선언이 명확할 수 있지만,
  사용하는 곳이 막연합니다.

  ~~~ swift
  func add(observer: NSObject, for keyPath: String)

  grid.add(self, for: graphics) // vague
  ~~~
  {:.bad}

  명확성을 복원하려면, **각각의 약 타입 매개변수와 매개변수 역할을 설명하는 명사를
  앞으로 보냅니다**:

  ~~~ swift
  func add**Observer**(_ observer: NSObject, for**KeyPath** path: String)
  grid.addObserver(self, forKeyPath: graphics) // clear
  ~~~
  {:.good}
  {{enddetail}}


### 유창한 사용을 하도록 노력하기(Strive for Fluent Usage)

* 사용하는 곳에 메소드와 함수 이름을 문법상 영어 문구 형태로 선호합니다.
  {:#methods-and-functions-read-as-phrases}
  
  {{expand}}
  {{detail}}  
  ~~~swift
  x.insert(y, at: z)          <span class="commentary">“x에서 z에다 y를 삽입”</span>
  x.subViews(havingColor: y)  <span class="commentary">“색상 y을 갖는 x의 subviews”</span>
  x.capitalizingNouns()       <span class="commentary">“x에 명사를 대문자화”</span>
  ~~~
  {:.good}
  
  ~~~swift
  x.insert(y, position: z)
  x.subViews(color: y)
  x.nounCapitalize()
  ~~~
  {:.bad}
  
  첫 번째 또는 두 번째 인자가 호출 의미에서 중심이 아닐 때 유창하도록
  인자 뒤를 떨어뜨리는 것을 허용합니다:

  ~~~swift
  AudioUnit.instantiate(
    with: description, 
    **options: [.inProcess], completionHandler: stopProgressBar**)
  ~~~
  {{enddetail}}

* **“`make`”로 팩토리 메소드 이름이 시작합니다.** e.g. `x.makeIterator()`
  {:#begin-factory-name-with-make}

* **이니셜라이저와
  [팩토리 메소드](https://en.wikipedia.org/wiki/Factory_method_pattern) 호출**은
  첫 번째 인자를 포함하지 않는 문구로 구성해야 합니다.
  e.g. `x.makeWidget(cogCount: 47)`
  {:#init-factory-phrase-ends-with-basename}

  {{expand}}
  {{detail}}
  예를 들어, 이러한 호출 때문에 내포된 문구는 첫 번째 인자를 포함하지 않습니다:

  ~~~swift
  let foreground = **Color**(red: 32, green: 64, blue: 128)
  let newPart = **factory.makeWidget**(gears: 42, spindles: 14)
  ~~~
  {:.good}
  
  다음에서, API 저자는 첫 번째 인자와 문법의 연속성을 만드는데 노력하고 있습니다.

  ~~~swift
  let foreground = **Color(havingRGBValuesRed: 32, green: 64, andBlue: 128)**
  let newPart = **factory.makeWidget(havingGearCount: 42, andSpindleCount: 14)**
  ~~~
  {:.bad}

  실제로, 이 가이드라인은 [인자 레이블](#argument-labels)과
  더불어 [full-width 타입 변환](#type-conversion)을
  수행하는 호출이 아니면 첫 번째 인자는 레이블을 가짐을 의미합니다.

  ~~~swift
  let rgbForeground = RGBColor(cmykForeground)
  ~~~
  {{enddetail}}

* **사이드 이펙트에 따라 함수와 메소드 이름을 지정합니다.**
  {:#name-according-to-side-effects}

  * 사이드 이펙트가 없는 것은 명사구로 읽어야 합니다.
    e.g. `x.distance(to: y)`, `i.successor()`.
  
  * 사이드 이펙트가 있는 것은 반드시 동사 구문으로 읽어야 합니다.
    e.g., `print(x)`, `x.sort()`, `x.append(y)`.

  * **Mutating/nonmutating 메소드 쌍을** 일관되게 **이름을 지정합니다**.
    mutating 메소드는 종종 비슷한 의미가 있는 nonmutating variant 있지만,
    인스턴스 in-place를 갱신하는 것보다 새로운 값을 반환하는 것이 낫습니다.
    
    * 연산자는 **당연히 동사로 설명**될 때,
      mutating 메소드는 동사를 반드시 사용하고
      nonmutating 메소드 이름을 지정하기 위해 “ed”나 “ing” 접미사를 적용합니다.
      
      |Mutating|Nonmutating|
      |-
      |`x.sort()`|`z = x.sorted()`|
      |`x.append(y)`|`z = x.appending(y)`|
      
      {{expand}}
      {{detail}}

      * 동사의 [과거분사](https://en.wikipedia.org/wiki/Participle)
        (보통은 “ed”를 붙임)를 사용하여 nonmutating variant 이름 지정을 선호합니다:

        ~~~ swift
        /// `self` in-place를 뒤집음.
        mutating func reverse()

        /// 뒤집혀진 `self`의 사본을 반환
        func revers**ed**() -> Self
        ...
        x.reverse()
        let y = x.reversed()
        ~~~

      * 동사가 목적어를 가져 추가한 “ed”가 문법적이지 않을 때,
        동사의 현재 [분사](https://en.wikipedia.org/wiki/Participle)를
        사용하여 nonmutating variant에
        “ing"을 붙여 이름을 지정합니다.

        ~~~ swift
        /// `self`에서 모든 줄바꿈을 떼어냄.
        mutating func stripNewlines()

        /// 모든 줄바꿈을 떼어낸 `self`의 사본을 반환.
        func strip**ping**Newlines() -> String
        ...
        s.stripNewlines()
        let oneLine = t.strippingNewlines()
        ~~~

      {{enddetail}}

    * 연산자는 당연히 명사로 설명될 때,
      nonmutating 메소드는 명사를 사용하고
      mutating 메소드는 “form” 접미사를 적용합니다.

      |Nonmutating|Mutating|
      |-
      |`x = y.union(z)`|`y.formUnion(z)`|
      |`j = c.successor(i)`|`c.formSuccessor(&i)`|

* **Boolean 메소드와 속성 사용은** nonmutating일 때
  **수신자에 관한 주장으로 해석해야 합니다**
  e.g. `x.isEmpty`, `line1.intersects(line2)`.
  {:#boolean-assertions}

* ***무언가를* 설명하는 프로토콜은 명사로 읽어야 합니다.**
  (e.g. `Collection`)
  {:#protocols-describing-what-is-should-read-as-nouns}

* ***능력*을 설명하는 프로토콜은 `able`, `ible` 또는 `ing` 접미사를
  사용하여 이름을 지정해야 합니다.**
  (e.g. `Equatable`, `ProgressReporting`).
  {:#protocols-describing-capability-should-use-suffixes}

* 다른 **타입, 속성, 변수 그리고 상수**의 이름은 **명사로 읽어야 합니다.**
  {:#name-of-others-should-read-as-nouns}

### 전문 용어를 잘 사용하기(Use Terminology Well)

**Term of Art**
: *명사* - 특정 영역이나 직업 내 정확하고 전문적인 의미가 있는 단어나 구.

* 더욱 일반적인 단어가 의미를 잘 전달할 수 있다면 **애매한 용어는 피합니다**.
  “피부(skin)”가 목적을 전달할 수 있다면 “표피(epidermis)를 언급하지 않습니다.”
  Terms of art는 주요한 커뮤니케이션 도구이지만,
  손실될 중요한 의미를 포착하는 데 사용되어야 합니다.
  {:#avoid-obscure-terms}

* Term of Art를 사용한다면 **기존의 의미에 충실합니다**.
  {:#stick-to-established-meaning}

  {{expand}}
  {{detail}}
  더 일반적인 단어보다 기술 용어를 사용하는 유일한 이유는
  기술 용어가 정확하게 표현하지만, 반면 모호하거나 불분명합니다.
  그러므로 API는 받아들여지는 의미에 따라 엄격하게 용어를 사용해야 합니다.

  * **전문가를 놀라게 하지 마세요**: 우리가 기존 용어에 새로운 의미를 고안한 것처럼
    보인다면 이미 용어에 익숙한 사람은 놀라고 성을 낼 것입니다.
    {:#do-not-surprise-an-expert}

  * **초보자를 혼란스럽게 하지 마세요**: 용어를 배우려고 하는 사람은
    웹 검색을 하고 전통적인 의미를 찾을 가능성이 있습니다.
    {:#do-not-confuse-a-beginner}
  {{enddetail}}

* **약어를 피하세요.** 특히 표준이 아닌 약어는 효과적으로 Term of Art이며,
  이해도에 따라 약어를 비 축약 형태로 번역합니다.
  {:#avoid-abbreviations}

  > 사용하는 약어에 의도된 의미는 웹 사이트에서 쉽게 찾을 수 있습니다.

* **선례를 받아들이세요.** 기존 문화에 적합성 비용에 있어
  모든 초보자를 위해 용어를 최적화하지 마세요.
  {:#embrace-precedent}

  {{expand}}
  {{detail}}
  연속 데이터 구조 이름은 `List`처럼 간단한 용어 사용보다 `Array`가 낫습니다.
  비록 초보자도 쉽게 `List`의 의미를 파악할 수 있지만 말이죠.
  현재 컴퓨팅에서 Array는 기초이고, 모든 프로그래머는 알고 있거나
  array가 무엇인지 곧 공부할 것입니다.
  대부분 프로그래머가 잘 알고 있는 용어를 사용하고,
  웹 검색과 질문으로 보상받을 것입니다.

  수학처럼 특정 프로그래밍 *도메인* 내에서
  `verticalPositionOnUnitCircleAtOriginOfEndOfRadiusWithAngle(x)` 처럼
  설명 문구보다 `sin(x)`처럼 넓게 사용되고 있는 선례 용어를 선호합니다.
  이 경우, 선례는 약어를 피하기 위한 가이드라인보다 중요합니다: 전체 단어가 `sine`이지만,
  “sin(*x*)”은 수십 년간 프로그래머 사이에서, 수백 년간 수학자 사이에서
  흔하게 사용되었습니다.
  {{enddetail}}

## 규칙(Conventions)

### 일반적인 규칙(General Conventions)

* **O(1)이 아닌 계산 속성의 복잡성을 문서로 만듭니다.** 사람들은 속성 접근이
  중요한 계산을 수반하지 않는다고 종종 가정하는데, 이는 저장 속성은
  심성모형(mental model)을 갖기 때문입니다. 가정이 어긋날 수 있을 때
  경고해야 합니다.
  {:#document-computed-property-complexity}

* **자유 함수(free functions)보다 메소드와 속성을 선호합니다.** 자유 함수는
  특별한 경우에만 사용됩니다.
  {:#prefer-method-and-properties-to-functions}

  {{expand}}
  {{detail}}

  1. 명백한 `self`가 없을 때:

     ~~~
     min(x, y, z)
     ~~~

  2. 함수가 제약되지 않은 제네릭일 때:

     ~~~
     print(x)unconstrained
     ~~~

  3. 함수 구문이 기존 도메인 표기의 일부일 때:

     ~~~
     sin(x)
     ~~~

  {{enddetail}}

* **case convention을 따릅니다.** 타입과 프로토콜의 이름은
  `UpperCamelCase`입니다. 그 외 모든 것은 `lowerCamelCase`입니다.
  {:#follow-case-conventions}

  {{expand}}
  {{detail}}

  미국식 영어에서 모두 대문자로 흔하게 표시하는
  [두문자어](https://en.wikipedia.org/wiki/Acronym)(Acronym과 initialism)는
  case convention을 따라 대문자이거나 소문자로 일관되어야 합니다.
  
  ~~~swift
  var **utf8**Bytes: [**UTF8**.CodeUnit]
  var isRepresentableAs**ASCII** = true
  var user**SMTP**Server: Secure**SMTP**Server
  ~~~
  
  다른 약어(acronym)는 일반적인 단어로 다뤄져야 합니다:
  
  ~~~swift
  var **radar**Detector: **Radar**Scanner
  var enjoys**Scuba**Diving = true
  ~~~
  {{enddetail}}

  
{% comment %}
* **문법상의 모호성을 의식합니다.** 많은 단어가 명사 또는 동사 중 하나의
  역할을 할 수 있습니다. e.g. “insert,” “record,” “contract,” “drink.”
  단어의 이중 역할이 API의 명확성에 어떻게 영향을 미칠지 고려합니다.
  {:#be-conscious-of-grammatical-ambiguity}
{% endcomment %}

* **메소드는** 같은 기본 의미를 공유할 때 또는 구분된 도메인에서 수행할 때
  **기본 이름을 공유할 수 있습니다.**
  {:#similar-methods-can-share-a-base-name}

  {{expand}}
  {{detail}}
  다음은 권장하는 예제로, 메소드는 본질적으로 같은 것을 수행할 수 있기 때문입니다:

  ~~~ swift
  extension Shape {
    /// `other`가 `self` 지역 내에 있는 경우 `true`를 반환.
    func **contains**(other: **Point**) -> Bool { ... }

    /// `other`가 `self` 지역 내에 완전히 있는 경우 `true`를 반환.
    func **contains**(other: **Shape**) -> Bool { ... }

    /// `other`가 `self` 지역 내에 있는 경우 `true`를 반환.
    func **contains**(other: **LineSegment**) -> Bool { ... }
  }
  ~~~
  {:.good}

  그리고 기하학 타입 또는 컬렉션은 별도의 도메인이기 때문에,
  같은 프로그램 내에서 괜찮습니다:

  ~~~ swift
  extension Collection where Element : Equatable {
    /// `self`가 `sought`와 같은 요소를 포함하는 경우 `true`를 반환.
    func **contains**(sought: Element) -> Bool { ... }
  }
  ~~~
  {:.good}

  그러나 `index` 메소드가 다른 의미가 있고, 다른 이름으로 되어 있어야 합니다:

  ~~~ swift
  extension Database {
    /// Database의 검색 index를 rebuild 함
    func **index**() { ... }

    /// 주어진 table에서 `n`번째 row를 반환.
    func **index**(n: Int, inTable: TableID) -> TableRow { ... }
  }
  ~~~
  {:.bad}

  마지막으로, “반환 타입에 오버로딩”을 피하세요. 이는 타입 추론이 있을 때
  모호성을 일으키기 때문입니다.

  ~~~ swift
  extension Box {
    /// `self`에 저장된 `Int`를 반환하고, 없으면 nil을 반환.
    func **value**() -> Int? { ... }

    /// `self`에 저장된 `String`을 반환하고, 없으면 nil을 반환.
    func **value**() -> String? { ... }
  }
  ~~~
  {:.bad}

  {{enddetail}}

### 매개 변수(Parameters)
{:#parameter-names}

~~~swift
func move(from **start**: Point, to **end**: Point)
~~~

* **문서를 제공하는 매개 변수 이름을 선택합니다.** 매개 변수 이름이 함수나 메소드의
  사용 시점에 드러나지 않더라도, 중요한 설명하는 역할을 수행합니다.
  {:#choose-parameter-names-to-serve-doc}

  {{expand}}
  {{detail}}
  문서를 쉽게 읽을 수 있도록 매개 변수 이름을 선택합니다. 예를 들어, 매개 변수 이름은
  문서를 자연스럽게 읽을 수 있도록 만듭니다:

  ~~~swift
  /// `**predicate**`를 만족하는 `self`의 요소를 포함하는 `Array`를 반환.
  func filter(_ **predicate**: (Element) -> Bool) -> [Generator.Element]

  /// 주어진 요소의 `**subRange**`를 `**newElements**`로 치환.
  mutating func replaceRange(_ **subRange**: Range<Index>, with **newElements**: [E])
  ~~~
  {:.good}

  그러나 매개 변수는 어색하고 문법에 맞지 않는 문서를 만듭니다:
  
  ~~~swift
  /// `**includedInResult**`를 만족하는 `self`의 요소를 포함하는 `Array를 반환.
  func filter(_ **includedInResult**: (Element) -> Bool) -> [Generator.Element]

  /// **`r`로 표시된 요소의 range**를 `**with**`의 컨텐츠로 치환.
  mutating func replaceRange(_ **r**: Range<Index>, **with**: [E])
  ~~~
  {:.bad}

  {{enddetail}}

* **기본 매개 변수**는 일반적인 사용을 단순화할 때 **이용합니다**. 하나만 흔히 사용하는
  값을 가진 매개 변수는 기본값 후보입니다.
  {:#take-advantage-of-defaulted-parameters}

  {{expand}}
  {{detail}}
  기본 인자는 관련 없는 정보를 숨김으로써 가독성을 향상합니다. 예:

  ~~~ swift
  let order = lastName.compare(
    royalFamilyName**, options: [], range: nil, locale: nil**)
  ~~~
  {:.bad}

  더 단순하게 될 수 있습니다:

  ~~~ swift
  let order = lastName.**compare(royalFamilyName)**
  ~~~
  {:.good}

  기본 인자는 일반적으로 메소드 집합(family) 사용보다 더 나은데, API를 이해하려고
  하는 사람에게 낮은 인지 부담을 지우기 때문입니다.

  ~~~ swift
  extension String {
    /// *...description...*
    public func compare(
       other: String, options: CompareOptions **= []**,
       range: Range<Index>? **= nil**, locale: Locale? **= nil**
    ) -> Ordering
  }
  ~~~
  {:.good}

  위는 간단하지 않을 수 있지만, 아래보다 더 간단합니다:

  ~~~ swift
  extension String {
    /// *...description 1...*
    public func **compare**(other: String) -> Ordering
    /// *...description 2...*
    public func **compare**(other: String, options: CompareOptions) -> Ordering
    /// *...description 3...*
    public func **compare**(
       other: String, options: CompareOptions, range: Range<Index>) -> Ordering
    /// *...description 4...*
    public func **compare**(
       other: String, options: StringCompareOptions,
       range: Range<Index>, locale: Locale) -> Ordering
  }
  ~~~
  {:.bad}

  메소드 집합의 모든 구성원은 유저마다 별도의 설명과 이해가 필요합니다.
  이들 사이에서 결정하려면, 메소드 집합 전부를 이해할 필요가 있습니다.
  그리고 가끔 놀라운 관계-예제로, `foo(bar: nil)`와 `foo()`은 항상
  동의어가 아니다-는 대부분 같은 문서에서 작은 차이를 찾는 지루한 과정을
  만듭니다. 기본으로 단일 메소드 사용은 훨씬 뛰어난 프로그래머 경험을 제공합니다.
  {{enddetail}}

* 매개 변수 목록의 **마지막 방향으로 매개 변수와 기본값의 위치를 선호합니다**.
  기본값이 없는 매개 변수는 보통 메소드의 의미에 더 중요하고,
  메소드가 호출된 곳에서 안정적인 사용의 초기 패턴을 제공합니다.
  {:#parameter-with-defaults-towards-the-end}

### 인자 레이블(Argument Labels)

~~~swift
func move(**from** start: Point, **to** end: Point)
x.move(**from:** x, **to:** y) 
~~~

* **인자가 유용하게 구별될 수 없을 때 모든 레이블은 생략합니다.**
  e.g. `min(number1, number2)`, `zip(sequence1, sequence2)`
  {:#no-labels-for-indistinguishable-arguments}
  
* **이니셜라이저에서 full-width 타입 변환을 수행하며, 첫 번째 인자 레이블은 생략합니다.**
  e.g. `Int64(someUInt32)`
  {:#type-conversion}
  
  {{expand}}
  {{detail}}
  첫 번째 인자는 항상 변환의 출처가 될 것입니다.

  ~~~
  extension String {
    // radix에서 `x`가 텍스트 표시로 변경
    init(**_** x: BigInt, radix: Int = 10)   <span class="commentary">← 처음 밑줄을 주의</span>
  }

  text = "The value is: "
  text += **String(veryLargeNumber)**
  text += " and in hexadecimal, it's"
  text += **String(veryLargeNumber, radix: 16)**
  ~~~

  “narrowing” 타입 변환에서 narrowing을 설명하는 label을 추천합니다.

  ~~~ swift
  extension UInt32 {
    /// 저장된 `value`를 가지는 인스턴스를 생성.
    init(**_** value: Int16)            <span class="commentary">← Widening이고 label이 아님</span>
    /// `source`의 가장 낮은 32bits를 가지는 인스턴스를 생성.
    init(**truncating** source: UInt64)
    /// `valueToApproximate`에 가장 가깝게 표시할 수 있는 추정을
    /// 가지는 인스턴스를 생성
    init(**saturating** valueToApproximate: UInt64)
  }
  ~~~
  {{enddetail}}

* **첫 번째 인자가 [전치사구](https://en.wikipedia.org/wiki/Adpositional_phrase#Prepositional_phrases)의
  부분 형태일 때, 인자 레이블이 주어집니다.** 인자 레이블은 보통
  [전치사](https://en.wikipedia.org/wiki/Preposition)로 시작해야 합니다.
  {:#give-prepositional-phrase-argument-label}
  
  {{expand}}
  {{detail}}
  처음 두 개의 인자는 하나의 추상화 일부를 나타낼 때 예외가 나타납니다.
  
  ~~~swift
  a.move(**toX:** b, **y:** c)
  a.fade(**fromRed:** b, **green:** c, **blue:** d)
  ~~~
  {:.bad}

  이런 경우, 전치사 *뒤에* 인자 레이블이 시작하고 명확한 추상화를 유지합니다.
  
  ~~~swift
  a.moveTo(**x:** b, **y:** c)
  a.fadeFrom(**red:** b, **green:** c, **blue:** d)
  ~~~
  {:.good}
  {{enddetail}}
  
* **반면, 첫 번째 인자는 문법적인 구문 일부 형태일 때, 레이블을 생략하고**, 기본 이름에
  선행 단어를 추가합니다. e.g. `x.addSubview(y)`
  {:#omit-first-argument-if-partial-phrase}

  {{expand}}
  {{detail}}
  이 가이드라인은 첫 번째 인자가 문법적인 구문 일부를 형성하지 않으면 레이블을
  가져야 함을 의미합니다.

  ~~~swift
  view.dismiss(**animated:** false)
  let text = words.split(**maxSplits:** 12)
  let studentsByName = students.sorted(**isOrderedBefore:** Student.namePrecedes)
  ~~~
  {:.good}

  구문은 올바른 의미를 전달하는 것이 중요함을 주의합니다. 다음은 문법적이 될 것이지만
  잘못 표현하는 것입니다.

  ~~~swift
  view.dismiss(false)   <span class="commentary">dismiss 안하나요? Bool을 dismiss하나요?</span>
  words.split(12)       <span class="commentary">숫자 12를 분리하나요?</span>
  ~~~
  {:.bad}

  기본값이 있는 인자 또한 생략할 수 있음을 유의하고, 이 경우에 문법적인 구문의
  일부를 형성하지 않습니다. 그래서 항상 레이블을 가지고 있어야 합니다.
  {{enddetail}}

* **다른 모든 인자는 레이블을 지정합니다**.

## 특별 지침(Special Instructions)

* **클로저 매개 변수와 튜플 멤버**가 API에서 나타난 곳에 **레이블을 지정합니다**.
  {:#label-closure-parameters}
  
  {{expand}}
  {{detail}}
  These names have
  이 이름은 설득력이 있고, 문서 주석에서 참조될 수 있으며,
  튜플 멤버에 풍부한 접근을 제공합니다.
  
  ~~~ swift
  /// 적어도 `requestedCapacity` 요소에 대해 uniquely-referenced storage를
  /// 유지하고 있음을 보증합니다.
  ///
  /// 더 많은 storage가 필요하다면, 할당하기 위해 최대로 정렬된 바이트 수와
  /// 같은 **`byteCount`**를 사용하여 `allocate`를 호출합니다.
  ///
  /// - 반환:
  ///   - **reallocated**: 메모리의 새로운 block이 할당되었다면
  ///     `true`를 반환.
  ///   - **capacityChanged**: `capability`가 없데이트 되었다면 `true`를 반환.
  mutating func ensureUniqueStorage(
    minimumCapacity requestedCapacity: Int, 
    allocate: (**byteCount:** Int) -> UnsafePointer&lt;Void&gt;
  ) -> (**reallocated:** Bool, **capacityChanged:** Bool)
  ~~~

  클로저에서 사용될 때 기술적으로 [인자 레이블](#argument-labels)이지만,
  레이블을 선택해야 하고 매개 변수 이름이었던 것처럼 문서에서 사용해야 합니다. 함수 본체에서
  클로저 호출은 첫 번째 인자를 포함하지 않는 기본 이름에서 구문을 시작하는 함수를
  일관되게 읽을 수 있습니다:

  ~~~ swift
  allocate(byteCount: newCount * elementSize)
  ~~~
  {{enddetail}}

* 오버로드(overload) 세트에서 모호성을 피하도록 **제약되지 않은 다형성에
  주의를 더 기울여야 합니다**.(e.g. `Any`, `AnyObject` 그리고 제약되지
  않은 제네릭 매개 변수)
  {:#unconstrained-polymorphism}

  {{expand}}
  {{detail}}
  예를 들어, 오버로드 세트를 고려합니다:

  ~~~ swift
  struct Array<Element> {
    /// `self.endIndex`에 `newElement`를 삽입.
    public mutating func append(newElement: Element)

    /// 순서대로 `self.endIndex`에 `newElements` 컨텐츠를 삽입.
    public mutating func append<
      S : SequenceType where S.Generator.Element == Element
    >(newElements: S)
  }
  ~~~
  {:.bad}

  이들 메소드는 의미론 집합을 구성하고, 인자 타입은 획연히 구별되도록 처음에 나타납니다.
  그러나 `Element`가 `Any`일 때, 하나의 요소는 요소의 시퀀스로서 같은 타입을
  가질 수 있습니다.

  ~~~ swift
  var values: [Any] = [1, "a"]
  values.append([2, 3, 4]) // [1, "a", [2, 3, 4]] or [1, "a", 2, 3, 4]?
  ~~~
  {:.bad}

  모호성을 제거하려면, 더 명시적으로 두 번째 오버로드 이름을 지정합니다.

  ~~~ swift
  struct Array {
    /// `self.endIndex`에 `newElement`를 삽입.
    public mutating func append(newElement: Element)

    /// 순서대로 `self.endIndex`에 `newElement` 컨텐츠를 삽입
    public mutating func append<
      S : SequenceType where S.Generator.Element == Element
    >(**contentsOf** newElements: S)
  }
  ~~~
  {:.good}

  새로운 이름이 문서 주석과 더 일치하는 방법을 알 수 있습니다. 이 경우에는,
  문서 주석을 작성하는 행위는 실제로 API 저자의 주의를 이슈로 가져옵니다.
  {{enddetail}}


<script>
var elements = document.querySelectorAll("pre code");
for (i in elements) {
    var element = elements[i];
    if (element.textContent) {
        element.innerHTML = element.textContent
            .replace(/\*\*([^\*]+)\*\*/g, "<strong>$1</strong>")
            .replace(/\*([^\*]+)\*/g, "<em>$1</em>");
    }
}
function show_or_hide_all(){
    var checkboxes = document.getElementsByClassName('detail');
    var button = document.getElementById('toggle');

    if(button.value == 'Expand all details now'){
        for (var i in checkboxes){
            checkboxes[i].checked = 'FALSE';
        }
        button.value = 'Collapse all details now'
    }else{
        for (var i in checkboxes){
            checkboxes[i].checked = '';
        }
        button.value = 'Expand all details now';
    }
}
</script>


