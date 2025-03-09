# String VS StringBuilder VS StringBuffer

#### String은 불변객체. 
변하지 않는 문자열은 String을 사용한다.
- 변하지 않는 문자열을 저장할 때에 적합.
[String이 불변객체인 이유](String이%20불변객체인%20이유.md)

#### StringBuilder(가변)
비동기방식이기 때문에, Single Thread의 환경하에서, 변화되는 문자열에 사용한다. 비동기 방식이기 때문에 처리속도는 제일 빠르다.


#### StringBuffer(가변)
동기방식으로 저장되기 때문에 멀티쓰레드로 접근하거나 문자열이 변경될 경우에 사용한다.
