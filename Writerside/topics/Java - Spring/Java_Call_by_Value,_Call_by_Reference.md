# Java Call by Value, Call by Reference

## Call by Value? Call by Reference?

프로그래밍 언어들의 메소드 매개변수 호출 방식에는 여러 가지가 있으며 호출 방식은  
언어마다 다르게 되어 있습니다. 대표적으로 C++은 Call by Reference를 사용합니다.  
( 매개변수를 넘겨주는 행위이기 때문에 Pass by Value, Pass by Reference 라고도 합니다 )

Call by Value는 함수의 인자를 전달할 때 '값을 전달하는 방식'이고  
Call by Reference는 '주소를 전달하는 방식'입니다. 굉장히 헷갈릴 수 있는 부분이고  
심지어 저는 내용을 정리하기 전까진 자바가 두 방식을 혼용하여 사용하는 줄 알았지만...
자바는 확실하게 Call by Value 방식을 사용합니다.

이제 자바가 어떤 방식으로 Value 전달을 하는지 알아보겠습니다.

## 자바는 Call by Value

Call by Value는 '값을 전달하는 방식'이며 다르게 말하면 **'값만 전달하는 방식'** 입니다!  
만약 함수 A에서 B로 int 변수를 전달한다고 했을 때, 넘겨받은 B에서 어떤 행동을 하던지  
변수에는 변함이 없습니다.

예시를 통해 확인해보면  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2F17de5a63-6c46-46f2-8dc2-b0f3952c67b6%2Fimage.png)  
이렇게 method_1과 method_2의 두 함수가 존재할 때, method1은  
a=10, b=5의 값을 넘겨주고 method2는 이 값을 다른 값으로 교체합니다.  
그 이후 다시 method1에서 이 변수를 출력했을 때 어떤 변화가 생길까요?

![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2F7f0ef1fd-c9cb-4b81-ac01-5c6429adb875%2Fimage.png)  
method1에선 a=10, b=5의 값을 전달했고 method2에선 이 값을 a=20, b=10으로 바꿔서  
사용했습니다. 하지만 method2를 마치고 돌아왔을 때 method1에서는 여전히 a=10, b=5입니다.  
이게 바로 자바가 Call by Value 방식을 사용하기 때문에 나오는 결과입니다!

그렇다면 자바의 기본 타입을 제외한 참조 타입에서는 어떠한 결과가 나올까요?  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Ffbc0940e-1b6c-463a-96ec-bb7eff839149%2Fimage.png)  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Fb1366ede-070d-45cc-86a9-e243613f1281%2Fimage.png)  
자바의 대표적인 참조 타입인 String을 사용했을 때 int로 테스트한것과 동일한 결과가 나왔습니다.  
하지만 왜 우리는 자바의 Call 방식을 헷갈려하는걸까요?

## 이건 Call by Reference?

![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2F556ea6a2-6a5d-4307-beeb-393a0a986e9d%2Fimage.png)  
위의 테스트를 조금 바꿔서, 이번엔 새롭게 선언한 클래스를 가지고 테스트를 해보겠습니다.  
예상대로라면 아까와 같이 method1의 person은 field값이 변하지 않아야 할까요?  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Fc188fe41-8e82-477d-a7d5-1a7a889a65c2%2Fimage.png)  
이번엔 아까와 다른 결과가 나왔습니다. 어째서 이런 결과가 나왔을까요?

## Reference Type의 동작 원리에 정답이

위의 결과와 아래의 결과를 모두 아우를 수 있는 일반화를 해보자면, 자바에서는  
Call by Value 방식을 사용한다고 했습니다. 그렇다면 int를 전달할 때와 Person을 전달할 떄는  
어떤 차이가 있었던걸까요?  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2F00259c6c-3f5b-4d3f-8db9-2e33fc45a152%2Fimage.png)  
자바에서는 Call by Value 방식을 수행할 때, 값을 넘겨받은 메소드에서  
**값을 복사하여 새로운 지역 변수에 저장합니다**. 이 때 Method2는  
**Method1의 변수를 사용한 것이 아니라, 자신이 새롭게 생성한 지역 변수**에  
Method1의 **변수 이름과 변수 값을 복사하여 사용**하는 것이죠!

이 때문에 아무리 Method2에서 A와 B의 값을 바꾸어도 Method1의 A, B에게  
영향을 끼칠 수 없습니다. 이 A, B 는 이름만 같을 뿐 다른 주소를 가지는  
별개의 친구들이기 때문입니다. 하지만 만약 자바가 Call by Reference 방식을 사용한다면  
A와 B에 영향을 끼칠 수 있습니다. 왜냐면 이 둘은 다른 변수가 아니라 같은 주소를  
공유하는 변수이기 때문입니다.

이렇게 기본 타입에서의 작동 방식은 명확해졌습니다. 그럼 좀전의 Person은  
어째서 그런 결과가 나왔을까요?

![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2F160db5c4-0b20-4536-88ea-119d46fe1b01%2Fimage.png)  
바로 참조 타입은 이러한 방식을 사용하기 때문입니다. **애초에 '참조 타입'인 이유가  
Heap Memory 영역에 생성된 객체의 주소값을 참조하기 때문에** '참조 타입' 이라고 불립니다.  
따라서 Method1에서 Method2로 넘겨준건 Person의 주소값이고,  
Method1이 가지고 있는 주소값과 동일한 주소값을 가지고 이 객체의 상태를 수정하면  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Fe96c41f3-caac-4fa1-9235-fc0521f3362f%2Fimage.png)  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Fb0a79183-1dde-457e-bbb2-919da82ca64c%2Fimage.png)  
당연히 두 Person은 동일한 주소를 참조하기 때문에 이러한 결과가 나옵니다!

![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Fbc5b6332-2f6d-43d1-bec5-4f3c86e1e037%2Fimage.png)  
그렇다면 이와 비교해서 method_2에서 new 키워드를 사용하여 새로운 객체를 생성하고  
이 주소를 참조하게 했을 땐 어떤 결과가 나올지 예상할 수 있겠죠?  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2Fa226694a-a6f6-4c8e-9142-d2e43e3646c3%2Fimage.png)  
![](https://velog.velcdn.com/images%2Fahnick%2Fpost%2F470fd23b-5b88-47fc-b54b-0a6daba6a315%2Fimage.png)  
이제 두 변수는 다른 주소를 사용하여 객체를 참조하기 때문에 이런 결과가 나옵니다 !

## 정리

Call by Value, Call by Reference 방식은 굉장히 헷갈리는 부분입니다.  
하지만 자바가 탄생하던 시기의 C, C++와 비교하여 어째서 Call by Value 방식을  
채택하였는지 감히 예상해 보자면 두 언어에 공통으로 존재하는 **개발자의 주소 제어 권한을  
제약**하고, JVM을 사용하기 때문에 좀 더 Safety한 작동 방식이 필요했기 때문에 이런  
Call by Value 방식을 사용하지 않았나 감히 추측해 봅니다 !
