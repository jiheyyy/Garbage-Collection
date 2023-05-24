# 가비지 컬렉션(Garbage-Collection)
## 가비지 컬렉션이란?
가비지 컬렉션(Garbage-Collection)은 프로그램 실행 중에 사용되지 않는 메모리를 자동으로 정리해주는 기능이다. 이를 통해 메모리 누수를 방지하고 프로그램의 안정성과 성능을 개선할 수 있다.


## 가비지 컬렉션의 필요성
:효율적인 메모리 관리와 안정성을 제공하여 프로그램의 개발과 실행을 편리하게 만들어주는 필수적인 기능

*  개발자가 명시적으로 메모리를 해제하지 않아도 되게 해줘 메모리 누수나 잘못된 메모리 해제로 인한 버그를 방지
* 개발 프로세스를 단순화하고 생산성을 향상
* 사용되지 않는 메모리를 탐지하고 해제함으로써 메모리 누수를 방지하여 메모리를 효율적으로 관
* 동적으로 할당된 메모리를 관리
* 사용되지 않는 메모리를 회수함으로써 메모리 공간을 최적화하여 프로그램 성능 향상

## 가비지 컬렉션의 단점
* STW(Stop-The-World)
  * GC를 수행하기 위해 JVM이 프로그램 실행을 멈추는 현상을 의미.
  * GC가 작동하는 동안 GC 관련 Thread를 제외한 모든 Thread는 멈추게 되어 서비스 이용에 차질이 생길 수 있다.
따라서 이 시간을 최소화 시키는 것이 쟁점이다.

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




