# 가비지 컬렉션(Garbage-Collection)
## 가비지 컬렉션이란?
가비지 컬렉션(Garbage-Collection)은 프로그램 실행 중에 사용되지 않는 메모리를 자동으로 정리해주는 기능이다. 이를 통해 메모리 누수를 방지하고 프로그램의 안정성과 성능을 개선할 수 있다.
![garbage-collection](https://github.com/jiheyyy/Garbage-Collection/assets/117721167/b560afad-a59b-462e-9645-cb7e58765580)


## 가비지 컬렉션의 필요성
:효율적인 메모리 관리와 안정성을 제공하여 프로그램의 개발과 실행을 편리하게 만들어주는 필수적인 기능

*  개발자가 명시적으로 메모리를 해제하지 않아도 되게 해줘 메모리 누수나 잘못된 메모리 해제로 인한 버그를 방지
* 개발 프로세스를 단순화하고 생산성을 향상
* 사용되지 않는 메모리를 탐지하고 해제함으로써 메모리 누수를 방지하여 메모리를 효율적으로 관
* 동적으로 할당된 메모리를 관리
* 사용되지 않는 메모리를 회수함으로써 메모리 공간을 최적화하여 프로그램 성능 향상



## Python의 GC 동작 방식
* Reference counting
>레퍼런스 카운팅 방식의 가비지 컬렉팅은, 어떤 객체가 참조되고있는 횟수를 카운팅하고 0이 될 경우 메모리에서 해제하는 방식이다.
* Generational garbage collection
>만약 어떤 동적 객체가 서로를 참조(순환참조)하고 있다면 Reference counting 방식에서는 해당 객체들은 메모리에서 해제될 수 없다. 이러한 경우를 방지하기 위해, 파이썬에서는 Generational garbage collection이 순환 참조를 탐지하고 메모리에서 해제해준다.

* GC 수행 과정
  * 새로운 객체가 생성되면, 메모리와 0세대에 객체를 할당한다. 이 때, 객체 수가 0세대 임계값보다 크면 collect_generations()가 호출된다.
  * collect_generations()은 0, 1, 2세대 모두에 대해서 검사를 수행하며 2세대부터 역순으로 진행한다. 
  * 각 세대에 대해 임계값을 검사한 후 GC를 수행한다. 



* 순환 참조 해결 순서
  *  각 객체의 gc_refs 필드를 레퍼런스 카운트와 같게 설정
  * 각 객체에서 참조하고 있는 다른 컨테이너 객체를 찾고, 참조되는 컨테이너의 gc_refs를 감소시킨다.
  * 어느 객체의 gc_refs가 0이 되면, 그 객체는 컨테이너 집합 내부에서 자기들끼리 참조하고 있다는 뜻이다.
  * 그 객체를 unreachable하다고 표시한 뒤 메모리에서 해제한다.



## 가비지 컬렉션의 단점
* STW(Stop-The-World)
  * GC를 수행하기 위해 JVM이 프로그램 실행을 멈추는 현상을 의미.
  * GC가 작동하는 동안 GC 관련 Thread를 제외한 모든 Thread는 멈추게 되어 서비스 이용에 차질이 생길 수 있다.
따라서 이 시간을 최소화 시키는 것이 쟁점이다.


## GC모듈 사용
|Method|목적|
|---|---|
|gc.get_count()|각 세대의 객체 수 확인|
|gc.set_threshold()|세대별 임계치 설정|
|gc.collect_generations()|모든 세대에 대해서 2세대부터 0세대까지의 순서로 확인하고, 임계치 초과시 collect() 호출|
|gc.collect()|가비지 컬렉션 수행하여 순환참조 객체를 메모리에서 해제|

##  예제코드
### GC 기본 동작 코드
<pre>
<code>
import gc

class MyClass:
    def __init__(self):
        print("객체가 생성되었습니다.")
        
    def __del__(self):
        print("객체가 소멸되었습니다.")

# 객체 생성
obj1 = MyClass()
obj2 = MyClass()

# obj1을 더 이상 참조하지 않음
obj1 = None

# GC 호출 (수동으로 호출하는 것은 일반적으로 권장되지 않지만, 예제를 위해 사용)
gc.collect()

# obj2를 더 이상 참조하지 않음
obj2 = None

# GC 호출
gc.collect()
</code>
</pre>
* MyClass라는 클래스를 정의하고, 객체 생성 및 소멸 메시지 출력하는 메서드를 구현합니다.
* MyClass 객체를 생성하고 참조 변수에 할당합니다.
* 더 이상 필요하지 않은 객체를 참조하지 않도록 참조 변수를 None으로 설정하고, GC를 호출하여 메모리 해제를 수행합니다.

### 메모리 누수가 발생된 GC코드
<pre>
<code>
import gc

class MyClass:
    def __init__(self):
        print("객체가 생성되었습니다.")

# 리스트 생성
my_list = []

# 메모리 누수가 발생하는 무한 루프
while True:
    obj = MyClass()
    my_list.append(obj)
    gc.collect()  # GC 호출하여 메모리 해제
    </code>
</pre>
* MyClass 클래스를 정의하고, 객체 생성 시 "객체가 생성되었습니다."라는 메시지를 출력합니다.
* 무한 루프를 실행하여 MyClass 객체를 생성하고 리스트에 추가합니다.
* GC를 호출하여 메모리를 해제하려고 시도하지만, 무한 루프로 인해 GC가 동작하지 못하고 메모리 누수가 발생합니다.




