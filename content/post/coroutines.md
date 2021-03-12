---
title: "Coroutines"
date: 2021-02-18T18:06:15+09:00
draft: false
toc: false
comments: true
categories:
- Unity
- csharp
tags:
- Unity
- csharp
- Coroutine
---


<!--more-->
## Coroutines?  
코루틴은 실행을 중단하고 유니티로 제어를 반환하는데 다음 프레임에서 중단 된 지점부터 실행을 계속할 수 있는 기능  
- 유니티 도큐먼트 > [Coroutines?](https://docs.unity3d.com/Manual/Coroutines.html)

기본값은 프레임이지만 프레임 단위가 아닌 시간 단위 또는 커스텀한 단위로 실행을 할 수 있다.  
unity optimize에 관심이 있다면 Update() 내에 호출 대신 Coroutines을 이용하란 말을 본 적이 있을거다.
매 프레임마다 체크해야할 게 아니라면 Coroutines으로 대체하면 어떨까?  
- 읽어볼만한 내용 [10000번의 Update() 호출](https://blogs.unity3d.com/kr/2015/12/23/1k-update-calls/)

### Start
MonoBehaviour.StartCoroutine()로 호출하면 된다. [문서](https://docs.unity3d.com/ScriptReference/MonoBehaviour.StartCoroutine.html)

    public Coroutine StartCoroutine(IEnumerator routine);
	
반환형을 보면 Coroutine 으로 선언되어 있는데 단순 참조용이고 속성이나 함수를 보유하지 않는다고 안내한다.  
> 원문:  Instances of this class are only used to reference these coroutines, and do not hold any exposed properties or functions.

따라서 제어를 위해 변수로 사용한다면 타입을 IEnumerator로 지정해야 한다.  

    Coroutine myCoroutine = StartCoroutine(); // X
	
	IEnumerator myCoroutine = StartCoroutine(); // O

### Stop
  1. StopCoroutine(), StopAllCoroutines() 호출로 코루틴을 중단할 수 있다.  
  2. 코루틴이 연결된 GameObject가 SetActive(false)로 비활성화 될경우 중단된다.  
  3. Destroy(MonoBehaviour 인스턴스) 호출 시 즉시 OnDisable()이 호출되고 코루틴이 처리되어 효과적으로 중지된다.
  4. OnDestroy()는 프레임 끝에서 호출됨.  

### Remarks
MonoBehaviour enabled 설정으로는 중단되지 않음

## Reference

### IEnumerator
유니티 도규먼트를 읽다보면 코루틴의의 반환형은 IEnumerator, 펑션 바디 어딘가에 yield return 문이 포함되어있고, 
yield return null 라인은 실행이 중지되고 다음 프레임에서 재개되는 포인트다 라는 말이 있다.
> 원문: It is essentially a function declared with a return type of IEnumerator and with the yield return statement included somewhere in the body. The yield return null line is the point at which execution will pause and be resumed the following frame. 

IEnumerator는 뭔데? 여기서부턴 C# 도큐먼트를 보자. >
[IEnumerator](https://docs.microsoft.com/en-us/dotnet/api/system.collections.ienumerator?redirectedfrom=MSDN&view=net-5.0)  
non-generic collection에 간단한 반복을 지원한다고 한다.

    public interface IEnumerator 
	{
	  // 현재 위치에 있는 컬렉션 요소를 반환
      public object Current { get; }
	
	  // 열거자를 컬렉션의 다음 요소로 이동
	  public bool MoveNext ();
	  // 열거자를 컬렉션의 첫 번재 요소 앞의 초기 위치로 설정
	  public void Reset ();
	}

문서를 읽다보면 foreach 문은 열거자의 복잡성을 숨겨주니 열거자를 직접 조작하기보다 foreach 사용을 추천한다 라는 내용이 있다.
> 원문: The foreach statement of the C# language (for each in Visual Basic) hides the complexity of the enumerators. Therefore, using foreach is recommended instead of directly manipulating the enumerator.

    foreach (Person p in peopleList)
	{
      Console.WriteLine(p.firstName + " " + p.lastName);
	}
	
    var enumerator = peopleList.GetEnumerator();
	
	while (enumerator.MoveNext())
	{
	  Console.WriteLine(enumerator.Current.firstName + " " + enumerator.Current.lastName);
	}

어쩌면 당연하게, 익숙하게 사용하던 구문이였지만 문서를 읽으면 이런 재미난 내용도 읽을 수 있고 좋다.

### yield
그럼 yield는 뭔데? 마찬가지로 c# 도큐먼트를 보자. >
[yield](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/yield)  
메소드, 연산자, get 접근자가 iterator(반복자)임을 나타낸다고 한다.

    public class PowersOf2
    {
      static void Main()
      {
        // Display powers of 2 up to the exponent of 8:
        foreach (int i in Power(2, 8))
        {
          Console.Write("{0} ", i);
        }
      }

      public static System.Collections.Generic.IEnumerable<int> Power(int number, int exponent)
      {
        int result = 1;

        for (int i = 0; i < exponent; i++)
        {
          result = result * number;
          yield return result;
        }
      }

      // Output: 2 4 8 16 32 64 128 256
    }
예제가 띠용하는 느낌도 있지만.. IEnumerator의 예제를 보고 yield의 예제를 보면 쉽게 이해할 수 있다.

---

마무리를 어떻게 지어야할지 모르겠지만 코루틴이 뭔진 쉽게 찾아볼 수 있고 사용법도 간단하다.  
하지만 한 번쯤 곱씹어 볼 필요는 있어보인다.


